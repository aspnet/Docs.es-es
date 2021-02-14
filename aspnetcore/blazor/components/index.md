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
ms.openlocfilehash: 823c24620b369874fdbc3e314b5b08952df83c8b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280110"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c5e22-103">Creación y uso de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5e22-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c5e22-104">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5e22-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c5e22-105">Las aplicaciones Blazor se crean usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="c5e22-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="c5e22-106">Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c5e22-107">Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c5e22-108">Además, son flexibles y ligeros,</span><span class="sxs-lookup"><span data-stu-id="c5e22-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="c5e22-109">y se pueden anidar, reutilizar y compartir entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c5e22-110">Clases de componentes</span><span class="sxs-lookup"><span data-stu-id="c5e22-110">Component classes</span></span>

<span data-ttu-id="c5e22-111">Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) (`.razor`) mediante una combinación de C# y marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="c5e22-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c5e22-112">Un componente de Blazor se conoce formalmente como *componente de Razor* .</span><span class="sxs-lookup"><span data-stu-id="c5e22-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="c5e22-113">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="c5e22-113">Razor syntax</span></span>

<span data-ttu-id="c5e22-114">Los componentes Razor de las aplicaciones Blazor usan de manera generalizada la sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="c5e22-115">Si no está familiarizado con el lenguaje de marcado de Razor, se recomienda leer la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="c5e22-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="c5e22-116">Al acceder al contenido de la sintaxis Razor, preste especial atención a las siguientes secciones:</span><span class="sxs-lookup"><span data-stu-id="c5e22-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="c5e22-117">[Directivas](xref:mvc/views/razor#directives): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analiza o funciona el marcado de los componentes.</span><span class="sxs-lookup"><span data-stu-id="c5e22-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="c5e22-118">[Atributos de directiva](xref:mvc/views/razor#directive-attributes): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analizan o funcionan los elementos de los componentes.</span><span class="sxs-lookup"><span data-stu-id="c5e22-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="c5e22-119">Nombres</span><span class="sxs-lookup"><span data-stu-id="c5e22-119">Names</span></span>

<span data-ttu-id="c5e22-120">El nombre de un componente debe empezar por mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="c5e22-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="c5e22-121">Por ejemplo, `MyCoolComponent.razor` no es válido, pero `myCoolComponent.razor` sí.</span><span class="sxs-lookup"><span data-stu-id="c5e22-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="c5e22-122">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="c5e22-122">Routing</span></span>

<span data-ttu-id="c5e22-123">El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c5e22-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="c5e22-124">Cuando se compila un archivo de Razor con una directiva [`@page`][9], la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> que especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="c5e22-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c5e22-125">En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="c5e22-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="c5e22-126">Para obtener más información, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="c5e22-127">marcado</span><span class="sxs-lookup"><span data-stu-id="c5e22-127">Markup</span></span>

<span data-ttu-id="c5e22-128">La interfaz de usuario de un componente se define mediante HTML.</span><span class="sxs-lookup"><span data-stu-id="c5e22-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c5e22-129">La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="c5e22-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="c5e22-130">Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c5e22-131">El nombre de la clase generada coincide con el nombre del archivo.</span><span class="sxs-lookup"><span data-stu-id="c5e22-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c5e22-132">Los miembros de la clase de componente se definen en un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="c5e22-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="c5e22-133">En el bloque [`@code`][1], el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c5e22-134">Se permite emplear más de un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="c5e22-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="c5e22-135">Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c5e22-136">Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="c5e22-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c5e22-137">En el siguiente ejemplo se evalúa y representa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c5e22-138">`headingFontStyle` del valor de la propiedad CSS de `font-style`</span><span class="sxs-lookup"><span data-stu-id="c5e22-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c5e22-139">`headingText` del contenido del elemento `<h1>`</span><span class="sxs-lookup"><span data-stu-id="c5e22-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c5e22-140">Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="c5e22-141">Blazor compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="c5e22-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="c5e22-142">En <xref:blazor/components/rendering> se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="c5e22-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="c5e22-143">Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="c5e22-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c5e22-144">Los componentes que generan páginas web suelen residir en la carpeta `Pages`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="c5e22-145">Los componentes que no son de página se colocan con frecuencia en la carpeta `Shared` o en una carpeta personalizada agregada al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c5e22-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="c5e22-146">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="c5e22-146">Namespaces</span></span>

<span data-ttu-id="c5e22-147">Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c5e22-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="c5e22-148">Así, si el espacio de nombres raíz de la aplicación es `BlazorSample` y el componente `Counter` reside en la carpeta `Pages`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="c5e22-149">El espacio de nombres del componente `Counter` es `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="c5e22-150">El nombre de tipo completo del componente es `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="c5e22-151">En el caso de las carpetas personalizadas que contienen componentes, agregue una directiva [`@using`][2] al componente principal o al archivo `_Imports.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c5e22-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="c5e22-152">En el ejemplo siguiente se ponen a disposición los componentes de la carpeta `Components`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="c5e22-153">También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="c5e22-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="c5e22-154">El espacio de nombres de un componente creado con Razor se basa en (por orden de prioridad):</span><span class="sxs-lookup"><span data-stu-id="c5e22-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="c5e22-155">Designación [`@namespace`][8] del marcado del archivo de Razor (`.razor`) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="c5e22-156">Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="c5e22-157">Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto (`.csproj`) y la ruta de acceso de la raíz del proyecto al componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="c5e22-158">Por ejemplo, el marco de trabajo resuelve `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) en el espacio de nombres `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="c5e22-159">Los componentes de C# siguen las reglas de los enlaces de nombres.</span><span class="sxs-lookup"><span data-stu-id="c5e22-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="c5e22-160">En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:</span><span class="sxs-lookup"><span data-stu-id="c5e22-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="c5e22-161">Ejecute `Pages` en la misma carpeta.</span><span class="sxs-lookup"><span data-stu-id="c5e22-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="c5e22-162">Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="c5e22-163">La calificación `global::` no se puede usar.</span><span class="sxs-lookup"><span data-stu-id="c5e22-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c5e22-164">No se pueden importar componentes con instrucciones [`using`](/dotnet/csharp/language-reference/keywords/using-statement) con alias (por ejemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c5e22-165">No se pueden usar nombres parciales.</span><span class="sxs-lookup"><span data-stu-id="c5e22-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c5e22-166">Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia al componente `NavMenu` (`NavMenu.razor`) con `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="c5e22-167">Compatibilidad parcial de clases</span><span class="sxs-lookup"><span data-stu-id="c5e22-167">Partial class support</span></span>

<span data-ttu-id="c5e22-168">Los componentes de Razor se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="c5e22-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="c5e22-169">Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="c5e22-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="c5e22-170">Se define código de C# en un bloque [`@code`][1] con marcado HTML y código de Razor en un solo archivo.</span><span class="sxs-lookup"><span data-stu-id="c5e22-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="c5e22-171">Las plantillas de Blazor definen sus componentes de Razor con este método.</span><span class="sxs-lookup"><span data-stu-id="c5e22-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="c5e22-172">El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.</span><span class="sxs-lookup"><span data-stu-id="c5e22-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="c5e22-173">En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque [`@code`][1] en una aplicación generada a partir de una plantilla de Blazor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="c5e22-174">El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:</span><span class="sxs-lookup"><span data-stu-id="c5e22-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="c5e22-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="c5e22-176">El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:</span><span class="sxs-lookup"><span data-stu-id="c5e22-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="c5e22-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="c5e22-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-178">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="c5e22-179">Agregue los espacios de nombres que sean necesarios al archivo de clase parcial.</span><span class="sxs-lookup"><span data-stu-id="c5e22-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="c5e22-180">Los espacios de nombres típicos usados por los componentes de Razor incluyen:</span><span class="sxs-lookup"><span data-stu-id="c5e22-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="c5e22-181">Las directivas [`@using`][2] del archivo `_Imports.razor` solo se aplican a los archivos Razor (`.razor`), por lo que no se aplican a los archivos C# (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="c5e22-182">Especificación de una clase base</span><span class="sxs-lookup"><span data-stu-id="c5e22-182">Specify a base class</span></span>

<span data-ttu-id="c5e22-183">La directiva [`@inherits`][6] se puede usar para especificar la clase base de un componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="c5e22-184">En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="c5e22-185">La clase base debe derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="c5e22-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="c5e22-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="c5e22-188">Uso de componentes</span><span class="sxs-lookup"><span data-stu-id="c5e22-188">Use components</span></span>

<span data-ttu-id="c5e22-189">Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="c5e22-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c5e22-190">El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c5e22-191">El siguiente marcado en `Pages/Index.razor` representa una instancia de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="c5e22-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="c5e22-193">Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado.</span><span class="sxs-lookup"><span data-stu-id="c5e22-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="c5e22-194">Al agregar una directiva [`@using`][2] relativa al espacio de nombres del componente, se permite que el componente esté disponible, lo que resuelve la advertencia.</span><span class="sxs-lookup"><span data-stu-id="c5e22-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="c5e22-195">Parámetros</span><span class="sxs-lookup"><span data-stu-id="c5e22-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="c5e22-196">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="c5e22-196">Route parameters</span></span>

<span data-ttu-id="c5e22-197">Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="c5e22-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="c5e22-198">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="c5e22-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c5e22-199">Se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="c5e22-199">Optional parameters are supported.</span></span> <span data-ttu-id="c5e22-200">En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="c5e22-201">Si el segmento no está presente, el valor de `Text` se establece en `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="c5e22-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c5e22-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="c5e22-204">No se admiten parámetros opcionales, por lo que en el ejemplo anterior se aplican dos directivas [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="c5e22-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="c5e22-205">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="c5e22-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c5e22-206">mientras que la segunda directiva [`@page`][9] recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="c5e22-207">Para obtener información sobre los parámetros de ruta comodín (`{*pageRoute}`), que capturan rutas de acceso en varios límites de carpeta, vea <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="c5e22-208">Parámetros del componente</span><span class="sxs-lookup"><span data-stu-id="c5e22-208">Component parameters</span></span>

<span data-ttu-id="c5e22-209">Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas simples o complejas en la clase del componente con el [atributo `[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="c5e22-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="c5e22-210">Use atributos para especificar argumentos para un componente en el marcado.</span><span class="sxs-lookup"><span data-stu-id="c5e22-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c5e22-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="c5e22-212">Se puede asignar un valor predeterminado a los parámetros de componente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="c5e22-213">En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c5e22-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-214">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="c5e22-215">Por convención, un valor de atributo que se compone de código de C# se asigna a un parámetro usando el [`@` símbolo reservado de Razor](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="c5e22-215">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="c5e22-216">Propiedad o campo primario: `Title="@{FIELD OR PROPERTY}`, donde el marcador de posición `{FIELD OR PROPERTY}` es un campo o propiedad de C# del componente primario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-216">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="c5e22-217">Resultado de un método: `Title="@{METHOD}"`, donde el marcador de posición `{METHOD}` es un método de C# del componente primario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-217">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="c5e22-218">[Expresión implícita o explícita](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, donde el marcador de posición `{EXPRESSION}` es una expresión de C#.</span><span class="sxs-lookup"><span data-stu-id="c5e22-218">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="c5e22-219">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="c5e22-219">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="c5e22-220">No cree componentes que escriban en sus propios *parámetros de componente*; en su lugar, use un campo privado.</span><span class="sxs-lookup"><span data-stu-id="c5e22-220">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="c5e22-221">Para obtener más información, vea la sección [Parámetros sobrescritos](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="c5e22-221">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="c5e22-222">Contenido secundario</span><span class="sxs-lookup"><span data-stu-id="c5e22-222">Child content</span></span>

<span data-ttu-id="c5e22-223">Los componentes pueden definir el contenido de otro componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-223">Components can set the content of another component.</span></span> <span data-ttu-id="c5e22-224">El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-224">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c5e22-225">En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment>, que representa a su vez un segmento de interfaz de usuario que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="c5e22-225">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="c5e22-226">El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse.</span><span class="sxs-lookup"><span data-stu-id="c5e22-226">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c5e22-227">El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.</span><span class="sxs-lookup"><span data-stu-id="c5e22-227">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c5e22-228">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-228">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c5e22-229">La propiedad que recibe el contenido de <xref:Microsoft.AspNetCore.Components.RenderFragment> debe denominarse `ChildContent` por convención.</span><span class="sxs-lookup"><span data-stu-id="c5e22-229">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c5e22-230">El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-230">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c5e22-231">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-231">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="c5e22-232">Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente secundario:</span><span class="sxs-lookup"><span data-stu-id="c5e22-232">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="c5e22-233">También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="c5e22-233">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="c5e22-234">Para obtener información sobre cómo se puede usar un <xref:Microsoft.AspNetCore.Components.RenderFragment> como plantilla para la interfaz de usuario del componente de Razor, consulte los siguientes artículos:</span><span class="sxs-lookup"><span data-stu-id="c5e22-234">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c5e22-235">Expansión de atributos y parámetros arbitrarios</span><span class="sxs-lookup"><span data-stu-id="c5e22-235">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c5e22-236">Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-236">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c5e22-237">Los atributos adicionales se pueden capturar en un diccionario y luego *expandirse* en un elemento cuando el componente se representa por medio de la directiva de Razor [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="c5e22-237">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="c5e22-238">Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="c5e22-238">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c5e22-239">Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.</span><span class="sxs-lookup"><span data-stu-id="c5e22-239">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c5e22-240">En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:</span><span class="sxs-lookup"><span data-stu-id="c5e22-240">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="c5e22-241">El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="c5e22-241">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="c5e22-242">Los elementos `<input>` representados con ambos métodos son idénticos:</span><span class="sxs-lookup"><span data-stu-id="c5e22-242">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="c5e22-243">Para aceptar atributos arbitrarios, defina un parámetro de componente usando el [atributo `[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) con la propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> establecida en `true`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-243">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c5e22-244">La propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro.</span><span class="sxs-lookup"><span data-stu-id="c5e22-244">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c5e22-245">Un componente solamente puede definir un parámetro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-245">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="c5e22-246">El tipo de propiedad que se usa con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> se debe poder asignar desde `Dictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="c5e22-246">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c5e22-247">`IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-247">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="c5e22-248">La posición de [`@attributes`][3] relativa a la posición de los atributos de elemento es importante.</span><span class="sxs-lookup"><span data-stu-id="c5e22-248">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="c5e22-249">Cuando [`@attributes`][3] se expande en el elemento, los atributos se procesan de derecha a izquierda (del último al primero).</span><span class="sxs-lookup"><span data-stu-id="c5e22-249">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="c5e22-250">Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-250">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="c5e22-251">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-251">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c5e22-252">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-252">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c5e22-253">El atributo `extra` del componente `Child` se establece a la derecha de [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="c5e22-253">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="c5e22-254">El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):</span><span class="sxs-lookup"><span data-stu-id="c5e22-254">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="c5e22-255">En el ejemplo siguiente, el orden de `extra` y [`@attributes`][3] se invierte en el elemento `Child` del componente `<div>`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-255">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="c5e22-256">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-256">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="c5e22-257">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-257">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="c5e22-258">El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="c5e22-258">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="c5e22-259">Captura de referencias a componentes</span><span class="sxs-lookup"><span data-stu-id="c5e22-259">Capture references to components</span></span>

<span data-ttu-id="c5e22-260">Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-260">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c5e22-261">Para capturar una referencia de componente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-261">To capture a component reference:</span></span>

* <span data-ttu-id="c5e22-262">Agregue un atributo [`@ref`][4] al componente secundario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-262">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="c5e22-263">Defina un campo con el mismo tipo que el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-263">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="c5e22-264">Cuando el componente se represente, el campo `loginDialog` se rellena con la instancia del componente secundario `CustomLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-264">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="c5e22-265">Tras ello, se pueden invocar métodos de .NET en la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-265">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c5e22-266">La variable `loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-266">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c5e22-267">Hasta que se represente el componente, no hay nada a lo que hacer referencia.</span><span class="sxs-lookup"><span data-stu-id="c5e22-267">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="c5e22-268">Para manipular las referencias de componente una vez finalizada la representación del componente, use los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="c5e22-268">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="c5e22-269">Para usar una variable de referencia con un controlador de eventos, use una expresión lambda o asigne el delegado de controlador de eventos en los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="c5e22-269">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="c5e22-270">Esto garantiza que la variable de referencia se asigna antes de que se asigne el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-270">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="c5e22-271">Para hacer referencias a componentes de un bucle, consulte el artículo sobre la [captura de referencias a varios componentes secundarios similares (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="c5e22-271">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="c5e22-272">Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5e22-272">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="c5e22-273">Las referencias de componente no se pasan al código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5e22-273">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="c5e22-274">Solo se usan en código .NET.</span><span class="sxs-lookup"><span data-stu-id="c5e22-274">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c5e22-275">**No** use referencias de componentes para mutar el estado de los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="c5e22-275">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c5e22-276">En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="c5e22-276">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c5e22-277">El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.</span><span class="sxs-lookup"><span data-stu-id="c5e22-277">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="c5e22-278">Contexto de sincronización</span><span class="sxs-lookup"><span data-stu-id="c5e22-278">Synchronization context</span></span>

<span data-ttu-id="c5e22-279">Blazor usa un contexto de sincronización (<xref:System.Threading.SynchronizationContext>) para aplicar un único subproceso lógico de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c5e22-279">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="c5e22-280">Los [métodos de ciclo de vida](xref:blazor/components/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en el contexto de sincronización.</span><span class="sxs-lookup"><span data-stu-id="c5e22-280">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="c5e22-281">El contexto de sincronización de Blazor Server intenta emular un entorno de un solo subproceso para que coincida estrechamente con el modelo WebAssembly en el explorador, que es de un solo subproceso.</span><span class="sxs-lookup"><span data-stu-id="c5e22-281">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="c5e22-282">En todo momento, el trabajo se realiza en exactamente un subproceso, lo que da la impresión de un único subproceso lógico.</span><span class="sxs-lookup"><span data-stu-id="c5e22-282">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="c5e22-283">Nunca se ejecutan dos operaciones simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-283">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="c5e22-284">Evitar las llamadas de bloqueo de subprocesos</span><span class="sxs-lookup"><span data-stu-id="c5e22-284">Avoid thread-blocking calls</span></span>

<span data-ttu-id="c5e22-285">Por lo general, no llame a los métodos siguientes.</span><span class="sxs-lookup"><span data-stu-id="c5e22-285">Generally, don't call the following methods.</span></span> <span data-ttu-id="c5e22-286">Los métodos siguientes bloquean el subproceso y, por tanto, impiden que la aplicación reanude el trabajo hasta que se complete <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="c5e22-286">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="c5e22-287">Invocación de métodos de componentes externamente para actualizar el estado</span><span class="sxs-lookup"><span data-stu-id="c5e22-287">Invoke component methods externally to update state</span></span>

<span data-ttu-id="c5e22-288">En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que envía al contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-288">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="c5e22-289">Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:</span><span class="sxs-lookup"><span data-stu-id="c5e22-289">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="c5e22-290">Registre `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-290">Register the `NotifierService`:</span></span>

* <span data-ttu-id="c5e22-291">En Blazor WebAssembly, registre el servicio como singleton en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-291">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="c5e22-292">En Blazor Server, registre el servicio como con ámbito en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-292">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="c5e22-293">Use el elemento `NotifierService` para actualizar un componente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-293">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="c5e22-294">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="c5e22-294">In the preceding example:</span></span>

* <span data-ttu-id="c5e22-295">`NotifierService` invoca el método `OnNotify` del componente fuera del contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-295">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="c5e22-296">`InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.</span><span class="sxs-lookup"><span data-stu-id="c5e22-296">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="c5e22-297">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-297">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="c5e22-298">El componente implementa <xref:System.IDisposable>, y el elemento delegado `OnNotify` anula su inscripción en el método `Dispose`, al que llama el marco cuando se desecha el componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-298">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="c5e22-299">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-299">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c5e22-300">Uso de \@key para controlar la conservación de elementos y componentes</span><span class="sxs-lookup"><span data-stu-id="c5e22-300">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c5e22-301">Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-301">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c5e22-302">Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.</span><span class="sxs-lookup"><span data-stu-id="c5e22-302">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c5e22-303">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-303">Consider the following example:</span></span>

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

<span data-ttu-id="c5e22-304">El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas.</span><span class="sxs-lookup"><span data-stu-id="c5e22-304">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c5e22-305">Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-305">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c5e22-306">Esto puede hacer que se genere una nueva representación más compleja de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="c5e22-306">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c5e22-307">En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.</span><span class="sxs-lookup"><span data-stu-id="c5e22-307">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c5e22-308">El proceso de asignación se puede controlar con el atributo de directiva [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="c5e22-308">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="c5e22-309">[`@key`][5] hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="c5e22-309">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="c5e22-310">Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-310">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c5e22-311">Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-311">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c5e22-312">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="c5e22-312">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c5e22-313">Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-313">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c5e22-314">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="c5e22-314">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c5e22-315">Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-315">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c5e22-316">En algunos escenarios, el uso de [`@key`][5] reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.</span><span class="sxs-lookup"><span data-stu-id="c5e22-316">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c5e22-317">Las claves son locales de cada componente o elemento contenedor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-317">Keys are local to each container element or component.</span></span> <span data-ttu-id="c5e22-318">Las claves no se comparan globalmente en todo el documento.</span><span class="sxs-lookup"><span data-stu-id="c5e22-318">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c5e22-319">Cuándo usar \@key</span><span class="sxs-lookup"><span data-stu-id="c5e22-319">When to use \@key</span></span>

<span data-ttu-id="c5e22-320">Normalmente, usar [`@key`][5] tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) y haya un valor adecuado para definir el elemento [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="c5e22-320">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="c5e22-321">También se puede usar [`@key`][5] para impedir que Blazor conserve un subárbol de componentes o elementos cuando un objeto cambie:</span><span class="sxs-lookup"><span data-stu-id="c5e22-321">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="c5e22-322">Si `@currentPerson` cambia, la directiva de atributo [`@key`][5] fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="c5e22-322">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c5e22-323">Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.</span><span class="sxs-lookup"><span data-stu-id="c5e22-323">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c5e22-324">Cuándo no usar \@key</span><span class="sxs-lookup"><span data-stu-id="c5e22-324">When not to use \@key</span></span>

<span data-ttu-id="c5e22-325">Las comparaciones con [`@key`][5] repercuten en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="c5e22-325">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="c5e22-326">El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar [`@key`][5] únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c5e22-326">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c5e22-327">Aun cuando [`@key`][5] no se use, Blazor conserva las instancias de componentes y elementos secundarios lo máximo posible.</span><span class="sxs-lookup"><span data-stu-id="c5e22-327">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c5e22-328">La única ventaja de utilizar [`@key`][5] es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.</span><span class="sxs-lookup"><span data-stu-id="c5e22-328">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c5e22-329">Qué valores usar en \@key</span><span class="sxs-lookup"><span data-stu-id="c5e22-329">What values to use for \@key</span></span>

<span data-ttu-id="c5e22-330">Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en [`@key`][5]:</span><span class="sxs-lookup"><span data-stu-id="c5e22-330">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="c5e22-331">Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="c5e22-331">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c5e22-332">Esto garantiza la conservación en función de la igualdad de las referencias de objetos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-332">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c5e22-333">Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-333">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c5e22-334">Asegúrese de que los valores usados en [`@key`][5] no entran en conflicto.</span><span class="sxs-lookup"><span data-stu-id="c5e22-334">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="c5e22-335">Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes.</span><span class="sxs-lookup"><span data-stu-id="c5e22-335">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="c5e22-336">Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="c5e22-336">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="c5e22-337">Parámetros sobrescritos</span><span class="sxs-lookup"><span data-stu-id="c5e22-337">Overwritten parameters</span></span>

<span data-ttu-id="c5e22-338">El marco Blazor impone con carácter general una asignación de parámetro de componentes primarios a secundarios segura:</span><span class="sxs-lookup"><span data-stu-id="c5e22-338">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="c5e22-339">Los parámetros no se sobrescriben de forma inesperada.</span><span class="sxs-lookup"><span data-stu-id="c5e22-339">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="c5e22-340">Los efectos secundarios se minimizan.</span><span class="sxs-lookup"><span data-stu-id="c5e22-340">Side-effects are minimized.</span></span> <span data-ttu-id="c5e22-341">Por ejemplo,se evitan representaciones adicionales, ya que pueden crear bucles de representación infinitos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-341">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="c5e22-342">Un componente secundario recibe nuevos valores de parámetro que posiblemente sobrescriban los valores existentes cuando el componente primario vuelva a representarse.</span><span class="sxs-lookup"><span data-stu-id="c5e22-342">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="c5e22-343">Sobrescribir valores de parámetro en un componente secundario de forma accidental suele producirse al desarrollar el componente con uno o varios parámetros enlazados a datos y cuando el desarrollador escribe directamente en un parámetro del elemento secundario:</span><span class="sxs-lookup"><span data-stu-id="c5e22-343">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="c5e22-344">El componente secundario se representa con uno o varios valores de parámetro del componente primario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-344">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="c5e22-345">El elemento secundario escribe directamente en el valor de un parámetro.</span><span class="sxs-lookup"><span data-stu-id="c5e22-345">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="c5e22-346">El componente primario vuelve a representar el valor del parámetro del elemento secundario y lo sobrescribe.</span><span class="sxs-lookup"><span data-stu-id="c5e22-346">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="c5e22-347">La posibilidad de sobrescribir valores de parámetro se extiende también a los establecedores de propiedades del componente secundario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-347">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="c5e22-348">**Nuestra orientación general es no crear componentes que se escriban directamente en sus propios parámetros.**</span><span class="sxs-lookup"><span data-stu-id="c5e22-348">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="c5e22-349">Considere el componente `Expander` erróneo siguiente que:</span><span class="sxs-lookup"><span data-stu-id="c5e22-349">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="c5e22-350">Representa el contenido secundario.</span><span class="sxs-lookup"><span data-stu-id="c5e22-350">Renders child content.</span></span>
* <span data-ttu-id="c5e22-351">Alterna la visualización del contenido secundario con un parámetro de componente (`Expanded`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-351">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="c5e22-352">El componente escribe directamente en el parámetro `Expanded`, que muestra el problema con los parámetros sobrescritos y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="c5e22-352">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="c5e22-353">El componente `Expander` se agrega a un componente principal que podría llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="c5e22-353">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="c5e22-354">Al principio, los componentes `Expander` se comportan de manera independiente cuando se alternan sus propiedades de `Expanded`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-354">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="c5e22-355">Los componentes secundarios mantienen sus estados según lo previsto.</span><span class="sxs-lookup"><span data-stu-id="c5e22-355">The child components maintain their states as expected.</span></span> <span data-ttu-id="c5e22-356">Cuando se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en el componente principal, el parámetro `Expanded` del primer componente secundario se restablece nuevamente en su valor inicial (`true`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-356">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="c5e22-357">No se restablece el valor `Expanded` del segundo componente de `Expander`, porque no se representa ningún contenido secundario en el segundo componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-357">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="c5e22-358">Para mantener el estado en el escenario anterior, use un *campo privado* en el componente `Expander` para mantener su estado de alternancia.</span><span class="sxs-lookup"><span data-stu-id="c5e22-358">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="c5e22-359">El siguiente componente `Expander` revisado:</span><span class="sxs-lookup"><span data-stu-id="c5e22-359">The following revised `Expander` component:</span></span>

* <span data-ttu-id="c5e22-360">Acepta el valor del parámetro de componente `Expanded` del componente principal.</span><span class="sxs-lookup"><span data-stu-id="c5e22-360">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="c5e22-361">Asigna el valor del parámetro de componente a un *campo privado* (`expanded`) en el [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="c5e22-361">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="c5e22-362">Usa el campo privado para mantener su estado de alternancia interno, que muestra cómo evitar escribir directamente en un parámetro.</span><span class="sxs-lookup"><span data-stu-id="c5e22-362">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="c5e22-363">Para obtener información adicional, consulte [Error de enlace bidireccional de Blazor (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="c5e22-363">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="c5e22-364">Aplicación de un atributo</span><span class="sxs-lookup"><span data-stu-id="c5e22-364">Apply an attribute</span></span>

<span data-ttu-id="c5e22-365">En los componentes de Razor se pueden aplicar atributos con la directiva [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="c5e22-365">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="c5e22-366">En el siguiente ejemplo se aplica el [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a la clase del componente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-366">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c5e22-367">Atributos de elementos HTML condicionales</span><span class="sxs-lookup"><span data-stu-id="c5e22-367">Conditional HTML element attributes</span></span>

<span data-ttu-id="c5e22-368">Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET.</span><span class="sxs-lookup"><span data-stu-id="c5e22-368">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c5e22-369">Si el valor es `false` o `null`, el atributo no se representa.</span><span class="sxs-lookup"><span data-stu-id="c5e22-369">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c5e22-370">Si el valor es `true`, el atributo se representa minimizado.</span><span class="sxs-lookup"><span data-stu-id="c5e22-370">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c5e22-371">En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:</span><span class="sxs-lookup"><span data-stu-id="c5e22-371">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c5e22-372">Si `IsCompleted` es `true`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="c5e22-372">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c5e22-373">Si `IsCompleted` es `false`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="c5e22-373">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c5e22-374">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="c5e22-374">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="c5e22-375">Algunos atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-375">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="c5e22-376">En esos casos, use un tipo `string` en lugar de `bool`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-376">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c5e22-377">HTML sin formato</span><span class="sxs-lookup"><span data-stu-id="c5e22-377">Raw HTML</span></span>

<span data-ttu-id="c5e22-378">Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal.</span><span class="sxs-lookup"><span data-stu-id="c5e22-378">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c5e22-379">Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-379">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c5e22-380">El valor se analiza como HTML o SVG y se inserta en el DOM.</span><span class="sxs-lookup"><span data-stu-id="c5e22-380">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e22-381">La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="c5e22-381">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c5e22-382">En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-382">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="c5e22-383">Plantillas de Razor</span><span class="sxs-lookup"><span data-stu-id="c5e22-383">Razor templates</span></span>

<span data-ttu-id="c5e22-384">Los fragmentos de representación se pueden definir mediante la sintaxis de plantilla de Razor.</span><span class="sxs-lookup"><span data-stu-id="c5e22-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c5e22-385">Las plantillas de Razor son una forma de definir un fragmento de interfaz de usuario y asumen el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="c5e22-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c5e22-386">En el siguiente ejemplo se muestra cómo especificar los valores <xref:Microsoft.AspNetCore.Components.RenderFragment> y <xref:Microsoft.AspNetCore.Components.RenderFragment%601> y las plantillas de representación directamente en un componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-386">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="c5e22-387">Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="c5e22-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="c5e22-388">Salida representada del código anterior:</span><span class="sxs-lookup"><span data-stu-id="c5e22-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="c5e22-389">Recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="c5e22-389">Static assets</span></span>

<span data-ttu-id="c5e22-390">Blazor sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [`web root (wwwroot)` del proyecto](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5e22-390">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="c5e22-391">Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático.</span><span class="sxs-lookup"><span data-stu-id="c5e22-391">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="c5e22-392">En el ejemplo siguiente, `logo.png` se encuentra físicamente en la carpeta `{PROJECT ROOT}/wwwroot/images`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-392">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="c5e22-393">Los componentes de Razor **no** admiten la notación de virgulilla-barra diagonal (`~/`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-393">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="c5e22-394">Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c5e22-394">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="c5e22-395">Las aplicaciones auxiliares de etiquetas no se admiten en los componentes</span><span class="sxs-lookup"><span data-stu-id="c5e22-395">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="c5e22-396">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) no se admiten en los componentes de Razor (archivos `.razor`).</span><span class="sxs-lookup"><span data-stu-id="c5e22-396">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="c5e22-397">Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c5e22-397">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="c5e22-398">Imágenes con formato Scalable Vector Graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="c5e22-398">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="c5e22-399">Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) (`.svg`) mediante la etiqueta `<img>`:</span><span class="sxs-lookup"><span data-stu-id="c5e22-399">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="c5e22-400">Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos (`.css`):</span><span class="sxs-lookup"><span data-stu-id="c5e22-400">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="c5e22-401">Pero no todos los escenarios admiten el uso de marcado SVG en línea.</span><span class="sxs-lookup"><span data-stu-id="c5e22-401">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="c5e22-402">Si se coloca una etiqueta `<svg>` directamente en un archivo de componente (`.razor`), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-402">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="c5e22-403">Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y [`@bind`][10] no se puede usar con algunas etiquetas SVG.</span><span class="sxs-lookup"><span data-stu-id="c5e22-403">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="c5e22-404">Para más información, consulte la [compatibilidad con SVG en Blazor(dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="c5e22-404">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="c5e22-405">Comportamiento de la representación de espacios en blanco</span><span class="sxs-lookup"><span data-stu-id="c5e22-405">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c5e22-406">A menos que se use la directiva [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) con un valor de `true`, el espacio en blanco adicional se quita de forma predeterminada si:</span><span class="sxs-lookup"><span data-stu-id="c5e22-406">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="c5e22-407">Está delante o detrás de un elemento.</span><span class="sxs-lookup"><span data-stu-id="c5e22-407">Leading or trailing within an element.</span></span>
* <span data-ttu-id="c5e22-408">Está delante o detrás de un parámetro `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-408">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="c5e22-409">Por ejemplo, el contenido secundario se pasa a otro componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-409">For example, child content passed to another component.</span></span>
* <span data-ttu-id="c5e22-410">Precede o sigue a un bloque de código de C#, como `@if` o `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-410">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="c5e22-411">La eliminación de espacios en blanco puede afectar a la salida representada cuando se usa una regla de CSS, como `white-space: pre`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-411">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="c5e22-412">Para deshabilitar esta optimización de rendimiento y conservar el espacio en blanco, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="c5e22-412">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="c5e22-413">Agregue la directiva `@preservewhitespace true` en la parte superior del archivo `.razor` para aplicar las preferencias a un componente específico.</span><span class="sxs-lookup"><span data-stu-id="c5e22-413">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="c5e22-414">Agregue la directiva `@preservewhitespace true` dentro de un archivo `_Imports.razor` para aplicar la preferencia a un subdirectorio completo o a todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="c5e22-414">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="c5e22-415">En la mayoría de los casos, no se requiere ninguna acción, ya que las aplicaciones normalmente seguirán funcionando con normalidad (pero más rápido).</span><span class="sxs-lookup"><span data-stu-id="c5e22-415">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="c5e22-416">Si la eliminación de espacios en blanco provoca problemas en un componente determinado, use `@preservewhitespace true` de ese componente para deshabilitar esta optimización.</span><span class="sxs-lookup"><span data-stu-id="c5e22-416">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c5e22-417">El espacio en blanco se conserva en el código fuente de un componente.</span><span class="sxs-lookup"><span data-stu-id="c5e22-417">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="c5e22-418">El texto de solo espacio en blanco se representa en el modelo Document Object Model (DOM) del explorador aunque no haya ningún efecto visual.</span><span class="sxs-lookup"><span data-stu-id="c5e22-418">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="c5e22-419">Considere el código de componente Razor siguiente:</span><span class="sxs-lookup"><span data-stu-id="c5e22-419">Consider the following Razor component code:</span></span>

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

<span data-ttu-id="c5e22-420">En el ejemplo anterior se representa el siguiente espacio en blanco innecesario:</span><span class="sxs-lookup"><span data-stu-id="c5e22-420">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="c5e22-421">Fuera del bloque de código `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-421">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="c5e22-422">Alrededor del elemento `<li>`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-422">Around the `<li>` element.</span></span>
* <span data-ttu-id="c5e22-423">Alrededor de la salida de `@item.Text`.</span><span class="sxs-lookup"><span data-stu-id="c5e22-423">Around the `@item.Text` output.</span></span>

<span data-ttu-id="c5e22-424">Una lista que contiene 100 elementos da como resultado 402 áreas de espacio en blanco, y ninguno de los espacios en blanco adicionales afecta visualmente a la salida representada.</span><span class="sxs-lookup"><span data-stu-id="c5e22-424">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="c5e22-425">Al representar HTML estático para componentes, no se conservaba el espacio en blanco dentro de una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="c5e22-425">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="c5e22-426">Por ejemplo, vea el origen del siguiente componente en la salida representada:</span><span class="sxs-lookup"><span data-stu-id="c5e22-426">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="c5e22-427">No se conserva el espacio en blanco del marcado de Razor anterior:</span><span class="sxs-lookup"><span data-stu-id="c5e22-427">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c5e22-428">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c5e22-428">Additional resources</span></span>

* <span data-ttu-id="c5e22-429"><xref:blazor/security/server/threat-mitigation>: incluye instrucciones sobre cómo crear aplicaciones de Blazor Server que deben afrontar situaciones de agotamiento de recursos.</span><span class="sxs-lookup"><span data-stu-id="c5e22-429"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "Razor Referencia de sintaxis para ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "Razor Referencia de sintaxis para ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "Razor Referencia de sintaxis para ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "Razor Referencia de sintaxis para ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "Razor Referencia de sintaxis para ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "Razor Referencia de sintaxis para ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "Razor Referencia de sintaxis para ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "Razor Referencia de sintaxis para ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "Razor Referencia de sintaxis para ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "Razor Referencia de sintaxis para ASP.NET Core"
