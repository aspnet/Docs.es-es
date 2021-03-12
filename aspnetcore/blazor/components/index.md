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
ms.openlocfilehash: 7048fe7107fb71d632bf12df9d53e6f168d5a0db
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587468"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="9ff9c-103">Creación y uso de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ff9c-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="9ff9c-104">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ff9c-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9ff9c-105">Las aplicaciones Blazor se crean usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="9ff9c-106">Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="9ff9c-107">Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="9ff9c-108">Además, son flexibles y ligeros,</span><span class="sxs-lookup"><span data-stu-id="9ff9c-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="9ff9c-109">y se pueden anidar, reutilizar y compartir entre proyectos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="9ff9c-110">Clases de componentes</span><span class="sxs-lookup"><span data-stu-id="9ff9c-110">Component classes</span></span>

<span data-ttu-id="9ff9c-111">Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) (`.razor`) mediante una combinación de C# y marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="9ff9c-112">Un componente de Blazor se conoce formalmente como *componente de Razor* .</span><span class="sxs-lookup"><span data-stu-id="9ff9c-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="9ff9c-113">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="9ff9c-113">Razor syntax</span></span>

<span data-ttu-id="9ff9c-114">Los componentes Razor de las aplicaciones Blazor usan de manera generalizada la sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="9ff9c-115">Si no está familiarizado con el lenguaje de marcado de Razor, se recomienda leer la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="9ff9c-116">Al acceder al contenido de la sintaxis Razor, preste especial atención a las siguientes secciones:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="9ff9c-117">[Directivas](xref:mvc/views/razor#directives): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analiza o funciona el marcado de los componentes.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="9ff9c-118">[Atributos de directiva](xref:mvc/views/razor#directive-attributes): palabras clave que van precedidas por `@` que normalmente cambian la manera en que se analizan o funcionan los elementos de los componentes.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="9ff9c-119">Nombres</span><span class="sxs-lookup"><span data-stu-id="9ff9c-119">Names</span></span>

<span data-ttu-id="9ff9c-120">El nombre de un componente debe empezar por mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="9ff9c-121">Por ejemplo, `MyCoolComponent.razor` no es válido, pero `myCoolComponent.razor` sí.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="9ff9c-122">Enrutamiento</span><span class="sxs-lookup"><span data-stu-id="9ff9c-122">Routing</span></span>

<span data-ttu-id="9ff9c-123">El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="9ff9c-124">Cuando se compila un archivo de Razor con una directiva [`@page`][9], la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> que especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="9ff9c-125">En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="9ff9c-126">Para obtener más información, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="9ff9c-127">marcado</span><span class="sxs-lookup"><span data-stu-id="9ff9c-127">Markup</span></span>

<span data-ttu-id="9ff9c-128">La interfaz de usuario de un componente se define mediante HTML.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="9ff9c-129">La lógica de representación dinámica (por ejemplo, bucles, condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada *Razor* .</span><span class="sxs-lookup"><span data-stu-id="9ff9c-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="9ff9c-130">Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="9ff9c-131">El nombre de la clase generada coincide con el nombre del archivo.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="9ff9c-132">Los miembros de la clase de componente se definen en un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="9ff9c-133">En el bloque [`@code`][1], el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="9ff9c-134">Se permite emplear más de un bloque [`@code`][1].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="9ff9c-135">Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="9ff9c-136">Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="9ff9c-137">En el siguiente ejemplo se evalúa y representa lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="9ff9c-138">`headingFontStyle` del valor de la propiedad CSS de `font-style`</span><span class="sxs-lookup"><span data-stu-id="9ff9c-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="9ff9c-139">`headingText` del contenido del elemento `<h1>`</span><span class="sxs-lookup"><span data-stu-id="9ff9c-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="9ff9c-140">Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="9ff9c-141">Blazor compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="9ff9c-142">En <xref:blazor/components/rendering> se proporcionan más detalles.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="9ff9c-143">Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="9ff9c-144">Los componentes que generan páginas web suelen residir en la carpeta `Pages`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="9ff9c-145">Los componentes que no son de página se colocan con frecuencia en la carpeta `Shared` o en una carpeta personalizada agregada al proyecto.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="9ff9c-146">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="9ff9c-146">Namespaces</span></span>

<span data-ttu-id="9ff9c-147">Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="9ff9c-148">Así, si el espacio de nombres raíz de la aplicación es `BlazorSample` y el componente `Counter` reside en la carpeta `Pages`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="9ff9c-149">El espacio de nombres del componente `Counter` es `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="9ff9c-150">El nombre de tipo completo del componente es `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="9ff9c-151">En el caso de las carpetas personalizadas que contienen componentes, agregue una directiva [`@using`][2] al componente principal o al archivo `_Imports.razor` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="9ff9c-152">En el ejemplo siguiente se ponen a disposición los componentes de la carpeta `Components`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="9ff9c-153">También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`][2]:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="9ff9c-154">El espacio de nombres de un componente creado con Razor se basa en (por orden de prioridad):</span><span class="sxs-lookup"><span data-stu-id="9ff9c-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="9ff9c-155">Designación [`@namespace`][8] del marcado del archivo de Razor (`.razor`) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="9ff9c-156">Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="9ff9c-157">Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto (`.csproj`) y la ruta de acceso de la raíz del proyecto al componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="9ff9c-158">Por ejemplo, el marco de trabajo resuelve `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) en el espacio de nombres `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="9ff9c-159">Los componentes de C# siguen las reglas de los enlaces de nombres.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="9ff9c-160">En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="9ff9c-161">Ejecute `Pages` en la misma carpeta.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="9ff9c-162">Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="9ff9c-163">La calificación `global::` no se puede usar.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="9ff9c-164">No se pueden importar componentes con instrucciones [`using`](/dotnet/csharp/language-reference/keywords/using-statement) con alias (por ejemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="9ff9c-165">No se pueden usar nombres parciales.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="9ff9c-166">Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia al componente `NavMenu` (`NavMenu.razor`) con `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="9ff9c-167">Compatibilidad parcial de clases</span><span class="sxs-lookup"><span data-stu-id="9ff9c-167">Partial class support</span></span>

<span data-ttu-id="9ff9c-168">Los componentes de Razor se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="9ff9c-169">Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="9ff9c-170">Se define código de C# en un bloque [`@code`][1] con marcado HTML y código de Razor en un solo archivo.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="9ff9c-171">Las plantillas de Blazor definen sus componentes de Razor con este método.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="9ff9c-172">El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="9ff9c-173">En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque [`@code`][1] en una aplicación generada a partir de una plantilla de Blazor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="9ff9c-174">El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="9ff9c-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="9ff9c-176">El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="9ff9c-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="9ff9c-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-178">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="9ff9c-179">Agregue los espacios de nombres que sean necesarios al archivo de clase parcial.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="9ff9c-180">Los espacios de nombres típicos usados por los componentes de Razor incluyen:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="9ff9c-181">Las directivas [`@using`][2] del archivo `_Imports.razor` solo se aplican a los archivos Razor (`.razor`), por lo que no se aplican a los archivos C# (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="9ff9c-182">Especificación de una clase base</span><span class="sxs-lookup"><span data-stu-id="9ff9c-182">Specify a base class</span></span>

<span data-ttu-id="9ff9c-183">La directiva [`@inherits`][6] se puede usar para especificar la clase base de un componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="9ff9c-184">En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="9ff9c-185">La clase base debe derivar de <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="9ff9c-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="9ff9c-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="9ff9c-188">Uso de componentes</span><span class="sxs-lookup"><span data-stu-id="9ff9c-188">Use components</span></span>

<span data-ttu-id="9ff9c-189">Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="9ff9c-190">El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="9ff9c-191">El siguiente marcado en `Pages/Index.razor` representa una instancia de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="9ff9c-192">`Shared/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-192">`Shared/HeadingComponent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

<span data-ttu-id="9ff9c-193">Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="9ff9c-194">Al agregar una directiva [`@using`][2] relativa al espacio de nombres del componente, se permite que el componente esté disponible, lo que resuelve la advertencia.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="9ff9c-195">Parámetros</span><span class="sxs-lookup"><span data-stu-id="9ff9c-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="9ff9c-196">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="9ff9c-196">Route parameters</span></span>

<span data-ttu-id="9ff9c-197">Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="9ff9c-198">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9ff9c-199">Se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-199">Optional parameters are supported.</span></span> <span data-ttu-id="9ff9c-200">En el ejemplo siguiente, el parámetro opcional `text` asigna el valor del segmento de ruta a la propiedad `Text` del componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="9ff9c-201">Si el segmento no está presente, el valor de `Text` se establece en `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="9ff9c-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9ff9c-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="9ff9c-204">No se admiten parámetros opcionales, por lo que en el ejemplo anterior se aplican dos directivas [`@page`][9].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="9ff9c-205">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="9ff9c-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9ff9c-206">mientras que la segunda directiva [`@page`][9] recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="9ff9c-207">Para obtener información sobre los parámetros de ruta comodín (`{*pageRoute}`), que capturan rutas de acceso en varios límites de carpeta, vea <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="9ff9c-208">Parámetros del componente</span><span class="sxs-lookup"><span data-stu-id="9ff9c-208">Component parameters</span></span>

<span data-ttu-id="9ff9c-209">Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas simples o complejas en la clase del componente con el [atributo `[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="9ff9c-210">Use atributos para especificar argumentos para un componente en el marcado.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="9ff9c-211">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-211">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

<span data-ttu-id="9ff9c-212">Se puede asignar un valor predeterminado a los parámetros de componente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="9ff9c-213">En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="9ff9c-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-214">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="9ff9c-215">Asigne campos, propiedades y métodos de C# a los parámetros del componente como valores de atributo HTML mediante el símbolos `@` reservado de [Razor ](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="9ff9c-215">Assign C# fields, properties, and methods to component parameters as HTML attribute values using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="9ff9c-216">Para asignar el campo, la propiedad o el método de un componente primario al parámetro de un componente secundario, anteponga el símbolo `@` al nombre del campo, de la propiedad o del método.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-216">To assign a parent component's field, property, or method to a child component's parameter, prefix the field, property, or method name with the `@` symbol.</span></span> <span data-ttu-id="9ff9c-217">Para asignar el resultado de una [expresión de C# implícita](xref:mvc/views/razor#implicit-razor-expressions) a un parámetro, anteponga un símbolo `@` a la expresión implícita.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-217">To assign the result of an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions) to a parameter, prefix the implicit expression with an `@` symbol.</span></span>

  <span data-ttu-id="9ff9c-218">El siguiente componente primario muestra cuatro instancias del componente `ChildComponent` anterior y establece sus valores de parámetro `Title` en:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-218">The following parent component displays four instances of the preceding `ChildComponent` component and sets their `Title` parameter values to:</span></span>

  * <span data-ttu-id="9ff9c-219">El valor del campo `title`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-219">The value of the `title` field.</span></span>
  * <span data-ttu-id="9ff9c-220">El resultado del método `GetTitle` de C#.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-220">The result of the `GetTitle` C# method.</span></span>
  * <span data-ttu-id="9ff9c-221">La fecha local actual en formato largo con <xref:System.DateTime.ToLongDateString%2A>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-221">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>.</span></span>
  * <span data-ttu-id="9ff9c-222">La propiedad `Name` del objeto `person`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-222">The `person` object's `Name` property.</span></span>

  <span data-ttu-id="9ff9c-223">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-223">`Pages/ParentComponent.razor`:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from field.
  </ChildComponent>
  
  <ChildComponent Title="@GetTitle()">
      Title from method.
  </ChildComponent>
  
  <ChildComponent Title="@DateTime.Now.ToLongDateString()">
      Title from implicit Razor expression.
  </ChildComponent>
  
  <ChildComponent Title="@person.Name">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title = "Panel Title from Parent";
      private Person person = new Person();
      
      private string GetTitle()
      {
          return "Panel Title from Parent";
      }
      
      private class Person
      {
          public string Name { get; set; } = "Dr. Who";
      }
  }
  ```
  
  <span data-ttu-id="9ff9c-224">A diferencia de lo que ocurre en Razor Pages (`.cshtml`), Blazor no puede realizar el trabajo asincrónico en una expresión de Razor mientras se representa un componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-224">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="9ff9c-225">Esto se debe a que Blazor está diseñado para representar interfaces de usuario interactivas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-225">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="9ff9c-226">En una interfaz de usuario interactiva, la pantalla debe mostrar siempre algo, por lo que no tiene sentido bloquear el flujo de representación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-226">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="9ff9c-227">En su lugar, el trabajo asincrónico se realiza durante uno de los [eventos asincrónicos del ciclo de vida](xref:blazor/components/lifecycle).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-227">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="9ff9c-228">Después de cada evento asincrónico del ciclo de vida, el componente puede representarse de nuevo.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-228">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="9ff9c-229">La sintaxis de Razor siguiente **no** se admite:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-229">The following Razor syntax is **not** supported:</span></span>
  
  ```razor
  <ChildComponent Title="@await ...">
      ...
  </ChildComponent>
  ```
  
  <span data-ttu-id="9ff9c-230">El código del ejemplo anterior genera un *error del compilador* si se compila la aplicación:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-230">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="9ff9c-231">El operador "await" solo se puede usar dentro de un método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-231">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="9ff9c-232">Marque este método con el modificador "Async" y cambie su tipo de valor devuelto a "Task".</span><span class="sxs-lookup"><span data-stu-id="9ff9c-232">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

  <span data-ttu-id="9ff9c-233">Para obtener un valor para el parámetro `Title` en el ejemplo anterior de manera asincrónica, el componente puede usar el evento del ciclo de vida [`OnInitializedAsync` ](xref:blazor/components/lifecycle#component-initialization-methods), como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-233">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods), as the following example demonstrates:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title;
      
      protected override async Task OnInitializedAsync()
      {
          title = await ...;
      }
  }
  ```
  
* <span data-ttu-id="9ff9c-234">Para asignar el resultado de una [expresión explícita de C#](xref:mvc/views/razor#explicit-razor-expressions) del componente primario al parámetro de un componente secundario, rodee la expresión entre paréntesis con un prefijo de símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-234">To assign the result of an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) in the parent component to a child component's parameter, surround the expression in parentheses with an `@` symbol prefix.</span></span>

  <span data-ttu-id="9ff9c-235">El siguiente componente secundario tiene un parámetro del componente <xref:System.DateTime>, `ShowItemsSinceDate`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-235">The following child component has a <xref:System.DateTime> component parameter, `ShowItemsSinceDate`.</span></span>
  
  <span data-ttu-id="9ff9c-236">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-236">`Shared/ChildComponent.razor`:</span></span>
  
  ```razor
  <div class="panel panel-default">
      <div class="panel-heading">Explicit DateTime Expression Example</div>
      <div class="panel-body">
          <p>@ChildContent</p>
          <p>One week ago date: @ShowItemsSinceDate</p>
      </div>
  </div>

  @code {
      [Parameter]
      public DateTime ShowItemsSinceDate { get; set; }

      [Parameter]
      public RenderFragment ChildContent { get; set; }
  }
  ```
  
  <span data-ttu-id="9ff9c-237">El componente primario siguiente calcula una fecha con una expresión explícita de C# que es una semana en el pasado para la asignación al parámetro `ShowItemsSinceDate` del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-237">The following parent component calculates a date with an explicit C# expression that's one week in the past for assignment to the child's `ShowItemsSinceDate` parameter.</span></span>
  
  <span data-ttu-id="9ff9c-238">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-238">`Pages/ParentComponent.razor`:</span></span>

  ```razor
  <ChildComponent ShowItemsSinceDate="@(DateTime.Now - TimeSpan.FromDays(7))">
      Title from explicit Razor expression.
  </ChildComponent>
  ```

  <span data-ttu-id="9ff9c-239">**No** se admite el uso de una expresión explícita para concatenar texto con el resultado de una expresión para la asignación a un parámetro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-239">Use of an explicit expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="9ff9c-240">En el ejemplo siguiente se busca concatenar el texto "SKU-" con un número de existencias (propiedad `SKU`, "Referencia de almacén") proporcionado por el objeto `product` de un componente primario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-240">The following example seeks to concatenate the text "SKU-" with a product stock number (`SKU` property, "Stock Keeping Unit") provided by a parent component's `product` object.</span></span> <span data-ttu-id="9ff9c-241">Aunque esta sintaxis se admite en una página de Razor (`.cshtml`), no es válida para la asignación al parámetro `Title` del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-241">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter.</span></span>
  
  ```razor
  <ChildComponent Title="SKU-@(product.SKU)">
      Title from composed Razor expression. This doesn't compile.
  </ChildComponent>
  ```
  
  <span data-ttu-id="9ff9c-242">El código del ejemplo anterior genera un *error del compilador* si se compila la aplicación:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-242">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="9ff9c-243">Los atributos del componente no admiten contenido complejo (C# y marcado combinado).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-243">Component attributes do not support complex content (mixed C# and markup).</span></span>
  
  <span data-ttu-id="9ff9c-244">Para admitir la asignación de un valor compuesto, utilice un método, un campo o una propiedad.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-244">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="9ff9c-245">En el ejemplo siguiente se realiza la concatenación de "SKU-" y el número de existencias de un producto en el método `GetTitle` de C#:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-245">The following example performs the concatination of "SKU-" and a product's stock number in the C# method `GetTitle`:</span></span>
  
  ```razor
  <ChildComponent Title="@GetTitle()">
      Composed title from method.
  </ChildComponent>
  
  @code {
      private Product product = new Product();

      private string GetTitle() => $"SKU-{product.SKU}";
      
      private class Product
      {
          public string SKU { get; set; } = "12345";
      }
  }
  ```
  
<span data-ttu-id="9ff9c-246">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-246">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="9ff9c-247">No cree componentes que escriban en sus propios *parámetros de componente*; en su lugar, use un campo privado.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-247">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="9ff9c-248">Para obtener más información, vea la sección [Parámetros sobrescritos](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-248">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

#### <a name="component-parameters-should-be-auto-properties"></a><span data-ttu-id="9ff9c-249">Los parámetros del componente deben ser propiedades automáticas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-249">Component parameters should be auto-properties</span></span>

<span data-ttu-id="9ff9c-250">Los parámetros del componente se deben declarar como *propiedades automáticas*, lo que significa que no deben contener lógica personalizada en sus captadores o establecedores.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-250">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their getters or setters.</span></span> <span data-ttu-id="9ff9c-251">Por ejemplo, la siguiente propiedad `StartData` es una propiedad automática:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-251">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="9ff9c-252">No coloque la lógica personalizada en el descriptor de acceso `get` o `set` porque los parámetros del componente están pensados exclusivamente para su uso como canal para que un componente primario envíe información a un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-252">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="9ff9c-253">Si un establecedor de una propiedad de componente secundario contiene lógica que provoca la repetición de la representación del componente primario, se produce un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-253">If a setter of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="9ff9c-254">Si necesita transformar un valor de parámetro recibido:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-254">If you need to transform a received parameter value:</span></span>

* <span data-ttu-id="9ff9c-255">Deje la propiedad del parámetro como propiedad automática pura para representar los datos sin procesar proporcionados.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-255">Leave the parameter property as a pure auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="9ff9c-256">Cree otra propiedad o método que proporcione los datos transformados en función de la propiedad del parámetro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-256">Create some other property or method that supplies the transformed data based on the parameter property.</span></span>

<span data-ttu-id="9ff9c-257">Puede invalidar `OnParametersSetAsync` si desea transformar un parámetro recibido cada vez que se reciben nuevos datos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-257">You can override `OnParametersSetAsync` if you want to transform a received parameter each time new data is received.</span></span>

## <a name="child-content"></a><span data-ttu-id="9ff9c-258">Contenido secundario</span><span class="sxs-lookup"><span data-stu-id="9ff9c-258">Child content</span></span>

<span data-ttu-id="9ff9c-259">Los componentes pueden definir el contenido de otro componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-259">Components can set the content of another component.</span></span> <span data-ttu-id="9ff9c-260">El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-260">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="9ff9c-261">En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento <xref:Microsoft.AspNetCore.Components.RenderFragment>, que representa a su vez un segmento de interfaz de usuario que se va a representar.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-261">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="9ff9c-262">El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-262">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="9ff9c-263">El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-263">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="9ff9c-264">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-264">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="9ff9c-265">La propiedad que recibe el contenido de <xref:Microsoft.AspNetCore.Components.RenderFragment> debe denominarse `ChildContent` por convención.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-265">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="9ff9c-266">El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-266">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="9ff9c-267">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-267">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="9ff9c-268">Debido a la forma en que Blazor representa el contenido secundario, la representación de componentes dentro de un bucle `for` requiere una variable de índice local si se usa la variable de bucle incremental en el contenido del componente secundario:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-268">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="9ff9c-269">También puede usar un bucle `foreach` con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-269">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="9ff9c-270">Para obtener información sobre cómo se puede usar un <xref:Microsoft.AspNetCore.Components.RenderFragment> como plantilla para la interfaz de usuario del componente de Razor, consulte los siguientes artículos:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-270">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="9ff9c-271">Expansión de atributos y parámetros arbitrarios</span><span class="sxs-lookup"><span data-stu-id="9ff9c-271">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="9ff9c-272">Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-272">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="9ff9c-273">Los atributos adicionales se pueden capturar en un diccionario y luego *expandirse* en un elemento cuando el componente se representa por medio de la directiva de Razor [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-273">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="9ff9c-274">Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-274">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="9ff9c-275">Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-275">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="9ff9c-276">En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-276">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="9ff9c-277">El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-277">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="9ff9c-278">Los elementos `<input>` representados con ambos métodos son idénticos:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-278">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="9ff9c-279">Para aceptar atributos arbitrarios, defina un parámetro de componente usando el [atributo `[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) con la propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> establecida en `true`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-279">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="9ff9c-280">La propiedad <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> en [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-280">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="9ff9c-281">Un componente solamente puede definir un parámetro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-281">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="9ff9c-282">El tipo de propiedad que se usa con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> se debe poder asignar desde `Dictionary<string, object>` con claves de cadena.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-282">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="9ff9c-283">`IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-283">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="9ff9c-284">La posición de [`@attributes`][3] relativa a la posición de los atributos de elemento es importante.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-284">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="9ff9c-285">Cuando [`@attributes`][3] se expande en el elemento, los atributos se procesan de derecha a izquierda (del último al primero).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-285">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="9ff9c-286">Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-286">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="9ff9c-287">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-287">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9ff9c-288">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-288">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9ff9c-289">El atributo `extra` del componente `Child` se establece a la derecha de [`@attributes`][3].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-289">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="9ff9c-290">El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):</span><span class="sxs-lookup"><span data-stu-id="9ff9c-290">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="9ff9c-291">En el ejemplo siguiente, el orden de `extra` y [`@attributes`][3] se invierte en el elemento `Child` del componente `<div>`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-291">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="9ff9c-292">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-292">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9ff9c-293">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-293">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9ff9c-294">El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-294">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="9ff9c-295">Captura de referencias a componentes</span><span class="sxs-lookup"><span data-stu-id="9ff9c-295">Capture references to components</span></span>

<span data-ttu-id="9ff9c-296">Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="9ff9c-297">Para capturar una referencia de componente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-297">To capture a component reference:</span></span>

* <span data-ttu-id="9ff9c-298">Agregue un atributo [`@ref`][4] al componente secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-298">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="9ff9c-299">Defina un campo con el mismo tipo que el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-299">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="9ff9c-300">Cuando el componente se represente, el campo `loginDialog` se rellena con la instancia del componente secundario `CustomLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-300">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="9ff9c-301">Tras ello, se pueden invocar métodos de .NET en la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9ff9c-302">La variable `loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="9ff9c-303">Hasta que se represente el componente, no hay nada a lo que hacer referencia.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-303">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="9ff9c-304">Para manipular las referencias de componente una vez finalizada la representación del componente, use los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-304">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="9ff9c-305">Para usar una variable de referencia con un controlador de eventos, use una expresión lambda o asigne el delegado de controlador de eventos en los métodos [`OnAfterRenderAsync` o `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-305">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="9ff9c-306">Esto garantiza que la variable de referencia se asigna antes de que se asigne el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-306">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="9ff9c-307">Para hacer referencias a componentes de un bucle, consulte el artículo sobre la [captura de referencias a varios componentes secundarios similares (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-307">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="9ff9c-308">Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-308">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="9ff9c-309">Las referencias de componente no se pasan al código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-309">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="9ff9c-310">Solo se usan en código .NET.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-310">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="9ff9c-311">**No** use referencias de componentes para mutar el estado de los componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-311">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="9ff9c-312">En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-312">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="9ff9c-313">El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-313">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="9ff9c-314">Contexto de sincronización</span><span class="sxs-lookup"><span data-stu-id="9ff9c-314">Synchronization context</span></span>

<span data-ttu-id="9ff9c-315">Blazor usa un contexto de sincronización (<xref:System.Threading.SynchronizationContext>) para aplicar un único subproceso lógico de ejecución.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-315">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="9ff9c-316">Los [métodos de ciclo de vida](xref:blazor/components/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en el contexto de sincronización.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-316">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="9ff9c-317">El contexto de sincronización de Blazor Server intenta emular un entorno de un solo subproceso para que coincida estrechamente con el modelo WebAssembly en el explorador, que es de un solo subproceso.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-317">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="9ff9c-318">En todo momento, el trabajo se realiza en exactamente un subproceso, lo que da la impresión de un único subproceso lógico.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-318">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="9ff9c-319">Nunca se ejecutan dos operaciones simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-319">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="9ff9c-320">Evitar las llamadas de bloqueo de subprocesos</span><span class="sxs-lookup"><span data-stu-id="9ff9c-320">Avoid thread-blocking calls</span></span>

<span data-ttu-id="9ff9c-321">Por lo general, no llame a los métodos siguientes.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-321">Generally, don't call the following methods.</span></span> <span data-ttu-id="9ff9c-322">Los métodos siguientes bloquean el subproceso y, por tanto, impiden que la aplicación reanude el trabajo hasta que se complete <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-322">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="9ff9c-323">Invocación de métodos de componentes externamente para actualizar el estado</span><span class="sxs-lookup"><span data-stu-id="9ff9c-323">Invoke component methods externally to update state</span></span>

<span data-ttu-id="9ff9c-324">En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que envía al contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-324">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="9ff9c-325">Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-325">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="9ff9c-326">Registre `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-326">Register the `NotifierService`:</span></span>

* <span data-ttu-id="9ff9c-327">En Blazor WebAssembly, registre el servicio como singleton en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-327">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="9ff9c-328">En Blazor Server, registre el servicio como con ámbito en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-328">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="9ff9c-329">Use el elemento `NotifierService` para actualizar un componente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-329">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="9ff9c-330">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-330">In the preceding example:</span></span>

* <span data-ttu-id="9ff9c-331">`NotifierService` invoca el método `OnNotify` del componente fuera del contexto de sincronización de Blazor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-331">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="9ff9c-332">`InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-332">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="9ff9c-333">Para obtener más información, vea <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-333">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="9ff9c-334">El componente implementa <xref:System.IDisposable>, y el elemento delegado `OnNotify` anula su inscripción en el método `Dispose`, al que llama el marco cuando se desecha el componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-334">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="9ff9c-335">Para obtener más información, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-335">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="9ff9c-336">Uso de \@key para controlar la conservación de elementos y componentes</span><span class="sxs-lookup"><span data-stu-id="9ff9c-336">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="9ff9c-337">Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-337">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="9ff9c-338">Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-338">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="9ff9c-339">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-339">Consider the following example:</span></span>

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

<span data-ttu-id="9ff9c-340">El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-340">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="9ff9c-341">Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-341">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="9ff9c-342">Esto puede hacer que se genere una nueva representación más compleja de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-342">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="9ff9c-343">En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-343">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="9ff9c-344">El proceso de asignación se puede controlar con el atributo de directiva [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-344">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="9ff9c-345">[`@key`][5] hace que el algoritmo de comparación garantice la conservación de elementos o componentes en función del valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-345">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="9ff9c-346">Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-346">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="9ff9c-347">Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-347">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="9ff9c-348">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-348">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9ff9c-349">Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-349">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="9ff9c-350">Las demás instancias permanecerán inalteradas.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-350">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9ff9c-351">Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-351">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="9ff9c-352">En algunos escenarios, el uso de [`@key`][5] reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-352">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9ff9c-353">Las claves son locales de cada componente o elemento contenedor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-353">Keys are local to each container element or component.</span></span> <span data-ttu-id="9ff9c-354">Las claves no se comparan globalmente en todo el documento.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-354">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="9ff9c-355">Cuándo usar \@key</span><span class="sxs-lookup"><span data-stu-id="9ff9c-355">When to use \@key</span></span>

<span data-ttu-id="9ff9c-356">Normalmente, usar [`@key`][5] tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) y haya un valor adecuado para definir el elemento [`@key`][5].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-356">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="9ff9c-357">También se puede usar [`@key`][5] para impedir que Blazor conserve un subárbol de componentes o elementos cuando un objeto cambie:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-357">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="9ff9c-358">Si `@currentPerson` cambia, la directiva de atributo [`@key`][5] fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-358">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="9ff9c-359">Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-359">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="9ff9c-360">Cuándo no usar \@key</span><span class="sxs-lookup"><span data-stu-id="9ff9c-360">When not to use \@key</span></span>

<span data-ttu-id="9ff9c-361">Las comparaciones con [`@key`][5] repercuten en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-361">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="9ff9c-362">El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar [`@key`][5] únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-362">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="9ff9c-363">Aun cuando [`@key`][5] no se use, Blazor conserva las instancias de componentes y elementos secundarios lo máximo posible.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-363">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="9ff9c-364">La única ventaja de utilizar [`@key`][5] es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-364">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="9ff9c-365">Qué valores usar en \@key</span><span class="sxs-lookup"><span data-stu-id="9ff9c-365">What values to use for \@key</span></span>

<span data-ttu-id="9ff9c-366">Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en [`@key`][5]:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-366">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="9ff9c-367">Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-367">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="9ff9c-368">Esto garantiza la conservación en función de la igualdad de las referencias de objetos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-368">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="9ff9c-369">Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-369">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="9ff9c-370">Asegúrese de que los valores usados en [`@key`][5] no entran en conflicto.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-370">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="9ff9c-371">Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-371">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="9ff9c-372">Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-372">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="9ff9c-373">Parámetros sobrescritos</span><span class="sxs-lookup"><span data-stu-id="9ff9c-373">Overwritten parameters</span></span>

<span data-ttu-id="9ff9c-374">El marco Blazor impone con carácter general una asignación de parámetro de componentes primarios a secundarios segura:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-374">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="9ff9c-375">Los parámetros no se sobrescriben de forma inesperada.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-375">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="9ff9c-376">Los efectos secundarios se minimizan.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-376">Side-effects are minimized.</span></span> <span data-ttu-id="9ff9c-377">Por ejemplo,se evitan representaciones adicionales, ya que pueden crear bucles de representación infinitos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-377">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="9ff9c-378">Un componente secundario recibe nuevos valores de parámetro que posiblemente sobrescriban los valores existentes cuando el componente primario vuelva a representarse.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-378">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="9ff9c-379">Sobrescribir valores de parámetro en un componente secundario de forma accidental suele producirse al desarrollar el componente con uno o varios parámetros enlazados a datos y cuando el desarrollador escribe directamente en un parámetro del elemento secundario:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-379">Accidentally overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="9ff9c-380">El componente secundario se representa con uno o varios valores de parámetro del componente primario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-380">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="9ff9c-381">El elemento secundario escribe directamente en el valor de un parámetro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-381">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="9ff9c-382">El componente primario vuelve a representar el valor del parámetro del elemento secundario y lo sobrescribe.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-382">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="9ff9c-383">La posibilidad de sobrescribir valores de parámetro se extiende también a los establecedores de propiedades del componente secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-383">The potential for overwriting parameter values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="9ff9c-384">**Nuestra orientación general es no crear componentes que se escriban directamente en sus propios parámetros.**</span><span class="sxs-lookup"><span data-stu-id="9ff9c-384">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="9ff9c-385">Considere el componente `Expander` erróneo siguiente que:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-385">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="9ff9c-386">Representa el contenido secundario.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-386">Renders child content.</span></span>
* <span data-ttu-id="9ff9c-387">Alterna la visualización del contenido secundario con un parámetro de componente (`Expanded`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-387">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="9ff9c-388">El componente escribe directamente en el parámetro `Expanded`, que muestra el problema con los parámetros sobrescritos y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-388">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="9ff9c-389">El componente `Expander` se agrega a un componente principal que podría llamar a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-389">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="9ff9c-390">Al principio, los componentes `Expander` se comportan de manera independiente cuando se alternan sus propiedades de `Expanded`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-390">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="9ff9c-391">Los componentes secundarios mantienen sus estados según lo previsto.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-391">The child components maintain their states as expected.</span></span> <span data-ttu-id="9ff9c-392">Cuando se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en el componente principal, el parámetro `Expanded` del primer componente secundario se restablece nuevamente en su valor inicial (`true`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-392">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="9ff9c-393">No se restablece el valor `Expanded` del segundo componente de `Expander`, porque no se representa ningún contenido secundario en el segundo componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-393">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="9ff9c-394">Para mantener el estado en el escenario anterior, use un *campo privado* en el componente `Expander` para mantener su estado de alternancia.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-394">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="9ff9c-395">El siguiente componente `Expander` revisado:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-395">The following revised `Expander` component:</span></span>

* <span data-ttu-id="9ff9c-396">Acepta el valor del parámetro de componente `Expanded` del componente principal.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-396">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="9ff9c-397">Asigna el valor del parámetro de componente a un *campo privado* (`expanded`) en el [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-397">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="9ff9c-398">Usa el campo privado para mantener su estado de alternancia interno, que muestra cómo evitar escribir directamente en un parámetro.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-398">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="9ff9c-399">Para obtener información adicional, consulte [Error de enlace bidireccional de Blazor (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-399">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="9ff9c-400">Aplicación de un atributo</span><span class="sxs-lookup"><span data-stu-id="9ff9c-400">Apply an attribute</span></span>

<span data-ttu-id="9ff9c-401">En los componentes de Razor se pueden aplicar atributos con la directiva [`@attribute`][7].</span><span class="sxs-lookup"><span data-stu-id="9ff9c-401">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="9ff9c-402">En el siguiente ejemplo se aplica el [atributo `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a la clase del componente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-402">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="9ff9c-403">Atributos de elementos HTML condicionales</span><span class="sxs-lookup"><span data-stu-id="9ff9c-403">Conditional HTML element attributes</span></span>

<span data-ttu-id="9ff9c-404">Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-404">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="9ff9c-405">Si el valor es `false` o `null`, el atributo no se representa.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-405">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="9ff9c-406">Si el valor es `true`, el atributo se representa minimizado.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-406">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="9ff9c-407">En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-407">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="9ff9c-408">Si `IsCompleted` es `true`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-408">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="9ff9c-409">Si `IsCompleted` es `false`, la casilla se representa así:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-409">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="9ff9c-410">Para obtener más información, consulte la [referencia sobre la sintaxis de Razor para ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-410">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="9ff9c-411">Algunos atributos HTML, como [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-411">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="9ff9c-412">En esos casos, use un tipo `string` en lugar de `bool`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-412">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="9ff9c-413">HTML sin formato</span><span class="sxs-lookup"><span data-stu-id="9ff9c-413">Raw HTML</span></span>

<span data-ttu-id="9ff9c-414">Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-414">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="9ff9c-415">Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-415">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="9ff9c-416">El valor se analiza como HTML o SVG y se inserta en el DOM.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-416">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="9ff9c-417">La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-417">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="9ff9c-418">En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-418">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="9ff9c-419">Plantillas de Razor</span><span class="sxs-lookup"><span data-stu-id="9ff9c-419">Razor templates</span></span>

<span data-ttu-id="9ff9c-420">Los fragmentos de representación se pueden definir mediante la sintaxis de plantilla de Razor.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-420">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="9ff9c-421">Las plantillas de Razor son una forma de definir un fragmento de interfaz de usuario y asumen el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-421">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="9ff9c-422">En el siguiente ejemplo se muestra cómo especificar los valores <xref:Microsoft.AspNetCore.Components.RenderFragment> y <xref:Microsoft.AspNetCore.Components.RenderFragment%601> y las plantillas de representación directamente en un componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-422">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="9ff9c-423">Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-423">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="9ff9c-424">Salida representada del código anterior:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-424">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="9ff9c-425">Recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="9ff9c-425">Static assets</span></span>

<span data-ttu-id="9ff9c-426">Blazor sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [`web root (wwwroot)` del proyecto](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-426">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="9ff9c-427">Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-427">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="9ff9c-428">En el ejemplo siguiente, `logo.png` se encuentra físicamente en la carpeta `{PROJECT ROOT}/wwwroot/images`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-428">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="9ff9c-429">Los componentes de Razor **no** admiten la notación de virgulilla-barra diagonal (`~/`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-429">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="9ff9c-430">Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-430">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="9ff9c-431">Las aplicaciones auxiliares de etiquetas no se admiten en los componentes</span><span class="sxs-lookup"><span data-stu-id="9ff9c-431">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="9ff9c-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) no se admiten en los componentes de Razor (archivos `.razor`).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="9ff9c-433">Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-433">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="9ff9c-434">Imágenes con formato Scalable Vector Graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="9ff9c-434">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="9ff9c-435">Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) (`.svg`) mediante la etiqueta `<img>`:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-435">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="9ff9c-436">Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos (`.css`):</span><span class="sxs-lookup"><span data-stu-id="9ff9c-436">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="9ff9c-437">Pero no todos los escenarios admiten el uso de marcado SVG en línea.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-437">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="9ff9c-438">Si se coloca una etiqueta `<svg>` directamente en un archivo de componente (`.razor`), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-438">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="9ff9c-439">Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y [`@bind`][10] no se puede usar con algunas etiquetas SVG.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-439">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="9ff9c-440">Para más información, consulte la [compatibilidad con SVG en Blazor(dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-440">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="9ff9c-441">Comportamiento de la representación de espacios en blanco</span><span class="sxs-lookup"><span data-stu-id="9ff9c-441">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9ff9c-442">A menos que se use la directiva [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) con un valor de `true`, el espacio en blanco adicional se quita de forma predeterminada si:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-442">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="9ff9c-443">Está delante o detrás de un elemento.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-443">Leading or trailing within an element.</span></span>
* <span data-ttu-id="9ff9c-444">Está delante o detrás de un parámetro `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-444">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="9ff9c-445">Por ejemplo, el contenido secundario se pasa a otro componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-445">For example, child content passed to another component.</span></span>
* <span data-ttu-id="9ff9c-446">Precede o sigue a un bloque de código de C#, como `@if` o `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-446">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="9ff9c-447">La eliminación de espacios en blanco puede afectar a la salida representada cuando se usa una regla de CSS, como `white-space: pre`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-447">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="9ff9c-448">Para deshabilitar esta optimización de rendimiento y conservar el espacio en blanco, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-448">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="9ff9c-449">Agregue la directiva `@preservewhitespace true` en la parte superior del archivo `.razor` para aplicar las preferencias a un componente específico.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-449">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="9ff9c-450">Agregue la directiva `@preservewhitespace true` dentro de un archivo `_Imports.razor` para aplicar la preferencia a un subdirectorio completo o a todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-450">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="9ff9c-451">En la mayoría de los casos, no se requiere ninguna acción, ya que las aplicaciones normalmente seguirán funcionando con normalidad (pero más rápido).</span><span class="sxs-lookup"><span data-stu-id="9ff9c-451">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="9ff9c-452">Si la eliminación de espacios en blanco provoca problemas en un componente determinado, use `@preservewhitespace true` de ese componente para deshabilitar esta optimización.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-452">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9ff9c-453">El espacio en blanco se conserva en el código fuente de un componente.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-453">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="9ff9c-454">El texto de solo espacio en blanco se representa en el modelo Document Object Model (DOM) del explorador aunque no haya ningún efecto visual.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-454">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="9ff9c-455">Considere el código de componente Razor siguiente:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-455">Consider the following Razor component code:</span></span>

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

<span data-ttu-id="9ff9c-456">En el ejemplo anterior se representa el siguiente espacio en blanco innecesario:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-456">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="9ff9c-457">Fuera del bloque de código `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-457">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="9ff9c-458">Alrededor del elemento `<li>`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-458">Around the `<li>` element.</span></span>
* <span data-ttu-id="9ff9c-459">Alrededor de la salida de `@item.Text`.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-459">Around the `@item.Text` output.</span></span>

<span data-ttu-id="9ff9c-460">Una lista que contiene 100 elementos da como resultado 402 áreas de espacio en blanco, y ninguno de los espacios en blanco adicionales afecta visualmente a la salida representada.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-460">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="9ff9c-461">Al representar HTML estático para componentes, no se conservaba el espacio en blanco dentro de una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-461">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="9ff9c-462">Por ejemplo, vea el origen del siguiente componente en la salida representada:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-462">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="9ff9c-463">No se conserva el espacio en blanco del marcado de Razor anterior:</span><span class="sxs-lookup"><span data-stu-id="9ff9c-463">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9ff9c-464">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9ff9c-464">Additional resources</span></span>

* <span data-ttu-id="9ff9c-465"><xref:blazor/security/server/threat-mitigation>: incluye instrucciones sobre cómo crear aplicaciones de Blazor Server que deben afrontar situaciones de agotamiento de recursos.</span><span class="sxs-lookup"><span data-stu-id="9ff9c-465"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

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
