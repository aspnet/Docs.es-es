---
title: Componentes de vista en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se usan los componentes de vista en ASP.NET Core y cómo agregarlos a las aplicaciones.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060591"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="3d6da-103">Componentes de vista en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d6da-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="3d6da-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3d6da-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3d6da-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d6da-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="3d6da-106">Componentes de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-106">View components</span></span>

<span data-ttu-id="3d6da-107">Los componentes de vista son similares a las vistas parciales, pero mucho más eficaces.</span><span class="sxs-lookup"><span data-stu-id="3d6da-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="3d6da-108">Los componentes de vista no usan el enlace de modelos y solo dependen de los datos proporcionados cuando se les llama.</span><span class="sxs-lookup"><span data-stu-id="3d6da-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="3d6da-109">Este artículo se ha escrito mediante controladores y vistas, pero los componentes de la vista también funcionan con Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="3d6da-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="3d6da-110">Un componente de vista:</span><span class="sxs-lookup"><span data-stu-id="3d6da-110">A view component:</span></span>

* <span data-ttu-id="3d6da-111">Representa un fragmento en lugar de una respuesta completa.</span><span class="sxs-lookup"><span data-stu-id="3d6da-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="3d6da-112">Incluye las mismas ventajas de separación de conceptos y capacidad de prueba que se encuentran entre un controlador y una vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="3d6da-113">Puede tener parámetros y lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="3d6da-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="3d6da-114">Normalmente se invoca desde una página de diseño.</span><span class="sxs-lookup"><span data-stu-id="3d6da-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="3d6da-115">Los componentes de vista están diseñados para cualquier lugar que tenga lógica de representación reutilizable demasiado compleja para una vista parcial, como:</span><span class="sxs-lookup"><span data-stu-id="3d6da-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="3d6da-116">Menús de navegación dinámica</span><span class="sxs-lookup"><span data-stu-id="3d6da-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="3d6da-117">Nube de etiquetas (donde consulta la base de datos)</span><span class="sxs-lookup"><span data-stu-id="3d6da-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="3d6da-118">Panel de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="3d6da-118">Login panel</span></span>
* <span data-ttu-id="3d6da-119">Carro de la compra</span><span class="sxs-lookup"><span data-stu-id="3d6da-119">Shopping cart</span></span>
* <span data-ttu-id="3d6da-120">Artículos publicados recientemente</span><span class="sxs-lookup"><span data-stu-id="3d6da-120">Recently published articles</span></span>
* <span data-ttu-id="3d6da-121">Contenido de la barra lateral de un blog típico</span><span class="sxs-lookup"><span data-stu-id="3d6da-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="3d6da-122">Un panel de inicio de sesión que se representa en cada página y muestra los vínculos para iniciar o cerrar sesión, según el estado del usuario</span><span class="sxs-lookup"><span data-stu-id="3d6da-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="3d6da-123">Un componente de vista consta de dos partes: la clase (normalmente derivada de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) y el resultado que devuelve (por lo general, una vista).</span><span class="sxs-lookup"><span data-stu-id="3d6da-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="3d6da-124">Al igual que los controladores, un componente de vista puede ser un POCO, pero la mayoría de los desarrolladores prefieren aprovechar las ventajas que ofrecen los métodos y las propiedades disponibles al derivar de `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="3d6da-125">Al considerar si los componentes de la vista cumplen las especificaciones de una aplicación, considere la posibilidad de usar Razor componentes en su lugar.</span><span class="sxs-lookup"><span data-stu-id="3d6da-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="3d6da-126">Razor Los componentes también combinan marcado con código C# para generar unidades de interfaz de usuario reutilizables.</span><span class="sxs-lookup"><span data-stu-id="3d6da-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="3d6da-127">Razor Los componentes están diseñados para la productividad de los desarrolladores a la hora de proporcionar lógica y composición de la interfaz de usuario del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="3d6da-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="3d6da-128">Para obtener más información, vea <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="3d6da-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="3d6da-129">Crear un componente de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-129">Creating a view component</span></span>

<span data-ttu-id="3d6da-130">Esta sección contiene los requisitos de alto nivel para crear un componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="3d6da-131">Más adelante en el artículo examinaremos cada paso en detalle y crearemos un componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="3d6da-132">La clase de componente de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-132">The view component class</span></span>

<span data-ttu-id="3d6da-133">Es posible crear una clase de componente de vista mediante una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="3d6da-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="3d6da-134">Derivar de *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="3d6da-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="3d6da-135">Decorar una clase con el atributo `[ViewComponent]` o derivar de una clase con el atributo `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="3d6da-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="3d6da-136">Crear una clase cuyo nombre termina con el sufijo *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="3d6da-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="3d6da-137">Al igual que los controladores, los componentes de vista deben ser clases públicas, no anidadas y no abstractas.</span><span class="sxs-lookup"><span data-stu-id="3d6da-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="3d6da-138">El nombre del componente de vista es el nombre de la clase sin el sufijo "ViewComponent".</span><span class="sxs-lookup"><span data-stu-id="3d6da-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="3d6da-139">También se puede especificar explícitamente mediante la propiedad `ViewComponentAttribute.Name`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="3d6da-140">Una clase de componente de vista:</span><span class="sxs-lookup"><span data-stu-id="3d6da-140">A view component class:</span></span>

* <span data-ttu-id="3d6da-141">Es totalmente compatible con la [inserción de dependencias](../../fundamentals/dependency-injection.md) de constructor.</span><span class="sxs-lookup"><span data-stu-id="3d6da-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="3d6da-142">No participa en el ciclo de vida del controlador, lo que significa que no se pueden usar [filtros](../controllers/filters.md) en un componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="3d6da-143">Métodos de componente de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-143">View component methods</span></span>

<span data-ttu-id="3d6da-144">Un componente de vista define su lógica en un método `InvokeAsync` que devuelve un elemento `Task<IViewComponentResult>` o en un método `Invoke` sincrónico que devuelve un elemento `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="3d6da-145">Los parámetros proceden directamente de la invocación del componente de vista, no del enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="3d6da-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="3d6da-146">Un componente de vista nunca controla directamente una solicitud.</span><span class="sxs-lookup"><span data-stu-id="3d6da-146">A view component never directly handles a request.</span></span> <span data-ttu-id="3d6da-147">Por lo general, un componente de vista inicializa un modelo y lo pasa a una vista mediante una llamada al método `View`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="3d6da-148">En resumen, los métodos de componente de vista:</span><span class="sxs-lookup"><span data-stu-id="3d6da-148">In summary, view component methods:</span></span>

* <span data-ttu-id="3d6da-149">Defina un método `InvokeAsync` que devuelva un elemento `Task<IViewComponentResult>` o un método `Invoke` sincrónico que devuelva un elemento `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="3d6da-150">Normalmente Inicializa un modelo y lo pasa a una vista mediante una llamada al `ViewComponent` `View` método.</span><span class="sxs-lookup"><span data-stu-id="3d6da-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="3d6da-151">Los parámetros provienen del método que realiza la llamada y no de HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d6da-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="3d6da-152">No hay ningún enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="3d6da-152">There's no model binding.</span></span>
* <span data-ttu-id="3d6da-153">No son accesibles directamente como punto de conexión HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d6da-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="3d6da-154">Se invocan desde el código (normalmente en una vista).</span><span class="sxs-lookup"><span data-stu-id="3d6da-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="3d6da-155">Un componente de vista nunca controla una solicitud.</span><span class="sxs-lookup"><span data-stu-id="3d6da-155">A view component never handles a request.</span></span>
* <span data-ttu-id="3d6da-156">Se sobrecargan en la firma, en lugar de los detalles de la solicitud HTTP actual.</span><span class="sxs-lookup"><span data-stu-id="3d6da-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="3d6da-157">Ruta de búsqueda de la vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-157">View search path</span></span>

<span data-ttu-id="3d6da-158">El tiempo de ejecución busca la vista en las rutas de acceso siguientes:</span><span class="sxs-lookup"><span data-stu-id="3d6da-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="3d6da-159">/Views/{Controller Name}/Components/{Nombre de componente de vista}/{Nombre de vista}</span><span class="sxs-lookup"><span data-stu-id="3d6da-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="3d6da-160">/Views/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}</span><span class="sxs-lookup"><span data-stu-id="3d6da-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="3d6da-161">/Pages/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}</span><span class="sxs-lookup"><span data-stu-id="3d6da-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="3d6da-162">La ruta de acceso de búsqueda se aplica a los proyectos mediante controladores + vistas y Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="3d6da-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="3d6da-163">El nombre de vista predeterminado para un componente de vista es *Default* , lo que significa que el archivo de vista normalmente se denominará *Default.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-163">The default view name for a view component is *Default* , which means your view file will typically be named *Default.cshtml* .</span></span> <span data-ttu-id="3d6da-164">Puede especificar un nombre de vista diferente al crear el resultado del componente de vista o al llamar al método `View`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="3d6da-165">Se recomienda que asigne el nombre *Default.cshtml* al archivo de vista y use la ruta de acceso *Views/Shared/Components/{Nombre de componente de vista}/{Nombre de vista}* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="3d6da-166">El componente de vista `PriorityList` usado en este ejemplo usa *Views/Shared/Components/PriorityList/Default.cshtml* para la vista del componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="3d6da-167">Personalización de la ruta de acceso de la búsqueda de la vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-167">Customize the view search path</span></span>

<span data-ttu-id="3d6da-168">Para personalizar la ruta de búsqueda de la vista, modifique la Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> colección de.</span><span class="sxs-lookup"><span data-stu-id="3d6da-168">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="3d6da-169">Por ejemplo, para buscar vistas dentro de la ruta de acceso "/Components/{Nombre del componente de la vista}/{Nombre de la vista}", agregue un elemento nuevo a la colección:</span><span class="sxs-lookup"><span data-stu-id="3d6da-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="3d6da-170">En el código anterior, el marcador de posición "{0}" representa la ruta de acceso "Components/{Nombre del componente de la vista}/{Nombre de la vista}".</span><span class="sxs-lookup"><span data-stu-id="3d6da-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="3d6da-171">Invocar un componente de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-171">Invoking a view component</span></span>

<span data-ttu-id="3d6da-172">Para usar el componente de vista, llame a lo siguiente dentro de una vista:</span><span class="sxs-lookup"><span data-stu-id="3d6da-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="3d6da-173">Los parámetros se pasarán al método `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="3d6da-174">El `PriorityList` componente de vista desarrollado en el artículo se invoca desde el archivo de vista *views/todo/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="3d6da-175">En la tabla siguiente, se llama al método `InvokeAsync` con dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="3d6da-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="3d6da-176">Invocación de un componente de vista como un asistente de etiquetas</span><span class="sxs-lookup"><span data-stu-id="3d6da-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="3d6da-177">Para ASP.NET Core 1.1 y versiones posteriores, puede invocar un componente de vista como un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="3d6da-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="3d6da-178">Los parámetros de clase y método con grafía Pascal para los asistentes de etiquetas se convierten a su [grafía kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="3d6da-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="3d6da-179">El asistente de etiquetas que va a invocar un componente de vista usa el elemento `<vc></vc>`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="3d6da-180">El componente de vista se especifica de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="3d6da-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="3d6da-181">Para usar un componente de vista como un asistente de etiquetas, registre el ensamblado que contiene el componente de vista mediante la directiva `@addTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="3d6da-182">Si el componente de vista está en un ensamblado denominado `MyWebApp`, agregue la directiva siguiente al archivo *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3d6da-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="3d6da-183">Puede registrar un componente de vista como un asistente de etiquetas en cualquier archivo que haga referencia al componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="3d6da-184">Vea [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) para más información sobre cómo registrar asistentes de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="3d6da-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="3d6da-185">El método `InvokeAsync` usado en este tutorial:</span><span class="sxs-lookup"><span data-stu-id="3d6da-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="3d6da-186">En el marcado del asistente de etiquetas:</span><span class="sxs-lookup"><span data-stu-id="3d6da-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="3d6da-187">En el ejemplo anterior, el componente de vista `PriorityList` se convierte en `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="3d6da-188">Los parámetros para el componente de vista se pasan como atributos en grafía kebab.</span><span class="sxs-lookup"><span data-stu-id="3d6da-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="3d6da-189">Invocar un componente de vista directamente desde un controlador</span><span class="sxs-lookup"><span data-stu-id="3d6da-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="3d6da-190">Los componentes de vista suelen invocarse desde una vista, pero se pueden invocar directamente desde un método de controlador.</span><span class="sxs-lookup"><span data-stu-id="3d6da-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="3d6da-191">Aunque los componentes de vista no definen puntos de conexión como controladores, se puede implementar fácilmente una acción del controlador que devuelva el contenido de `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="3d6da-192">En este ejemplo, se llama al componente de vista directamente desde el controlador:</span><span class="sxs-lookup"><span data-stu-id="3d6da-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="3d6da-193">Tutorial: Creación de un componente de vista simple</span><span class="sxs-lookup"><span data-stu-id="3d6da-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="3d6da-194">[Descargue](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), compile y pruebe el código de inicio.</span><span class="sxs-lookup"><span data-stu-id="3d6da-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="3d6da-195">Se trata de un proyecto simple con un controlador `ToDo` que muestra una lista de *tareas pendientes* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Lista de tareas pendientes](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="3d6da-197">Agregar una clase ViewComponent</span><span class="sxs-lookup"><span data-stu-id="3d6da-197">Add a ViewComponent class</span></span>

<span data-ttu-id="3d6da-198">Cree una carpeta *ViewComponents* y agregue la siguiente clase `PriorityListViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="3d6da-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="3d6da-199">Notas sobre el código:</span><span class="sxs-lookup"><span data-stu-id="3d6da-199">Notes on the code:</span></span>

* <span data-ttu-id="3d6da-200">Las clases de componentes de vista pueden estar contenidas en **cualquier** carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="3d6da-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="3d6da-201">Dado que el nombre de clase PriorityList **ViewComponent** acaba con el sufijo **ViewComponent** , el tiempo de ejecución usará la cadena "PriorityList" cuando haga referencia al componente de clase desde una vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-201">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent** , the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="3d6da-202">Más adelante explicaremos esta cuestión con más detalle.</span><span class="sxs-lookup"><span data-stu-id="3d6da-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="3d6da-203">El atributo `[ViewComponent]` puede cambiar el nombre usado para hacer referencia a un componente de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="3d6da-204">Por ejemplo, podríamos haber asignado a la clase el nombre `XYZ` y aplicado el atributo `ViewComponent`:</span><span class="sxs-lookup"><span data-stu-id="3d6da-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="3d6da-205">El atributo `[ViewComponent]` anterior le indica al selector de componentes de vista que use el nombre `PriorityList` al buscar las vistas asociadas al componente y que use la cadena "PriorityList" al hacer referencia al componente de clase desde una vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="3d6da-206">Más adelante explicaremos esta cuestión con más detalle.</span><span class="sxs-lookup"><span data-stu-id="3d6da-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="3d6da-207">El componente usa la [inserción de dependencias](../../fundamentals/dependency-injection.md) para que el contexto de datos esté disponible.</span><span class="sxs-lookup"><span data-stu-id="3d6da-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="3d6da-208">`InvokeAsync` expone un método al que se puede llamar desde una vista y puede tomar un número arbitrario de argumentos.</span><span class="sxs-lookup"><span data-stu-id="3d6da-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="3d6da-209">El método `InvokeAsync` devuelve el conjunto de elementos `ToDo` que cumplen los parámetros `isDone` y `maxPriority`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="3d6da-210">Crear la vista de componentes de vista Razor</span><span class="sxs-lookup"><span data-stu-id="3d6da-210">Create the view component Razor view</span></span>

* <span data-ttu-id="3d6da-211">Cree la carpeta *Views/Shared/Components* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="3d6da-212">Esta carpeta **debe** denominarse *Components* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-212">This folder **must** be named *Components* .</span></span>

* <span data-ttu-id="3d6da-213">Cree la carpeta *Views/Shared/Components/PriorityList* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="3d6da-214">El nombre de esta carpeta debe coincidir con el nombre de la clase de componente de vista o con el nombre de la clase sin el sufijo (si se ha seguido la convención y se ha usado el sufijo *ViewComponent* en el nombre de clase).</span><span class="sxs-lookup"><span data-stu-id="3d6da-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="3d6da-215">Si ha usado el atributo `ViewComponent`, el nombre de clase debe coincidir con la designación del atributo.</span><span class="sxs-lookup"><span data-stu-id="3d6da-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="3d6da-216">Cree una vista *views/Shared/Components/PriorityList/default. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="3d6da-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="3d6da-217">La Razor vista toma una lista de `TodoItem` y las muestra.</span><span class="sxs-lookup"><span data-stu-id="3d6da-217">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="3d6da-218">Si el método `InvokeAsync` del componente de vista no pasa el nombre de la vista (como en nuestro ejemplo), se usa *Default* para el nombre de vista, según la convención.</span><span class="sxs-lookup"><span data-stu-id="3d6da-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="3d6da-219">Más adelante en el tutorial veremos cómo pasar el nombre de la vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="3d6da-220">Para reemplazar el estilo predeterminado de un controlador concreto, agregue una vista a la carpeta de vistas específicas del controlador (por ejemplo, *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span></span>

    <span data-ttu-id="3d6da-221">Si el componente de vista es específico del controlador, puede agregarlo a la carpeta específica del controlador ( *views/todo/Components/PriorityList/default. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="3d6da-221">If the view component is controller-specific, you can add it to the controller-specific folder ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span></span>

* <span data-ttu-id="3d6da-222">Agregue un elemento `div` que contenga una llamada al componente de lista de prioridad en la parte inferior del archivo *Views/ToDo/index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3d6da-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="3d6da-223">El marcado `@await Component.InvokeAsync` muestra la sintaxis para llamar a los componentes de vista.</span><span class="sxs-lookup"><span data-stu-id="3d6da-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="3d6da-224">El primer argumento es el nombre del componente que se quiere invocar o llamar.</span><span class="sxs-lookup"><span data-stu-id="3d6da-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="3d6da-225">Los parámetros siguientes se pasan al componente.</span><span class="sxs-lookup"><span data-stu-id="3d6da-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="3d6da-226">`InvokeAsync` puede tomar un número arbitrario de argumentos.</span><span class="sxs-lookup"><span data-stu-id="3d6da-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="3d6da-227">Probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3d6da-227">Test the app.</span></span> <span data-ttu-id="3d6da-228">En la imagen siguiente se muestra la lista de tareas pendientes y los elementos de prioridad:</span><span class="sxs-lookup"><span data-stu-id="3d6da-228">The following image shows the ToDo list and the priority items:</span></span>

![lista de tareas pendientes y elementos de prioridad](view-components/_static/pi.png)

<span data-ttu-id="3d6da-230">También puede llamar al componente de vista directamente desde el controlador:</span><span class="sxs-lookup"><span data-stu-id="3d6da-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![elementos de prioridad de la acción IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="3d6da-232">Especificar un nombre de vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-232">Specifying a view name</span></span>

<span data-ttu-id="3d6da-233">En algunos casos, puede ser necesario que un componente de vista complejo especifique una vista no predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3d6da-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="3d6da-234">En el código siguiente se muestra cómo especificar la vista "PVC" desde el método `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="3d6da-235">Actualice el método `InvokeAsync` en la clase `PriorityListViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="3d6da-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="3d6da-236">Copie el archivo *Views/Shared/Components/PriorityList/Default.cshtml* en una vista denominada *Views/Shared/Components/PriorityList/PVC.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml* .</span></span> <span data-ttu-id="3d6da-237">Agregue un encabezado para indicar que se está usando la vista PVC.</span><span class="sxs-lookup"><span data-stu-id="3d6da-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="3d6da-238">Actualice *Views/ToDo/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3d6da-238">Update *Views/ToDo/Index.cshtml* :</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="3d6da-239">Ejecute la aplicación y compruebe la vista PVC.</span><span class="sxs-lookup"><span data-stu-id="3d6da-239">Run the app and verify PVC view.</span></span>

![Componente de vista de prioridad](view-components/_static/pvc.png)

<span data-ttu-id="3d6da-241">Si la vista PVC no se representa, compruebe que está llamando al componente de vista con prioridad cuatro o superior.</span><span class="sxs-lookup"><span data-stu-id="3d6da-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="3d6da-242">Examinar la ruta de acceso de la vista</span><span class="sxs-lookup"><span data-stu-id="3d6da-242">Examine the view path</span></span>

* <span data-ttu-id="3d6da-243">Cambie el parámetro de prioridad a tres o menos para que no se devuelva la vista de prioridad.</span><span class="sxs-lookup"><span data-stu-id="3d6da-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="3d6da-244">Cambie temporalmente el nombre de *views/todo/Components/PriorityList/default. cshtml* a *1Default. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml* .</span></span>
* <span data-ttu-id="3d6da-245">Pruebe la aplicación. Obtendrá el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="3d6da-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="3d6da-246">Copie *Views/ToDo/Components/PriorityList/1Default.cshtml* en *Views/Shared/Components/PriorityList/Default.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml* .</span></span>
* <span data-ttu-id="3d6da-247">Agregue algún marcado a la vista de componentes de la vista todo *compartida* para indicar que la vista procede de la carpeta *compartida* .</span><span class="sxs-lookup"><span data-stu-id="3d6da-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="3d6da-248">Pruebe la vista de componentes **Shared** .</span><span class="sxs-lookup"><span data-stu-id="3d6da-248">Test the **Shared** component view.</span></span>

![Salida de la lista de tareas pendientes con la vista de componentes Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="3d6da-250">Evitar cadenas codificadas de forma rígida</span><span class="sxs-lookup"><span data-stu-id="3d6da-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="3d6da-251">Si busca seguridad en tiempo de compilación, puede reemplazar el nombre del componente de vista codificado de forma rígida por el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="3d6da-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="3d6da-252">Cree el componente de vista sin el sufijo "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="3d6da-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="3d6da-253">Agregue una `using` instrucción al Razor archivo de vista y use el `nameof` operador:</span><span class="sxs-lookup"><span data-stu-id="3d6da-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="3d6da-254">Realizar el trabajo sincrónico</span><span class="sxs-lookup"><span data-stu-id="3d6da-254">Perform synchronous work</span></span>

<span data-ttu-id="3d6da-255">El marco controla la invocación de un método `Invoke` sincrónico si no necesita realizar un trabajo asincrónico.</span><span class="sxs-lookup"><span data-stu-id="3d6da-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="3d6da-256">El método siguiente crea un componente de vista `Invoke` sincrónico:</span><span class="sxs-lookup"><span data-stu-id="3d6da-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="3d6da-257">En el archivo del componente de vista Razor se enumeran las cadenas que se pasan al `Invoke` método ( *views/Home/Components/PriorityList/default. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="3d6da-257">The view component's Razor file lists the strings passed to the `Invoke` method ( *Views/Home/Components/PriorityList/Default.cshtml* ):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="3d6da-258">El componente de vista se invoca en un Razor archivo (por ejemplo, *views/home/index. cshtml* ) mediante uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="3d6da-258">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml* ) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="3d6da-259">Asistente de etiquetas</span><span class="sxs-lookup"><span data-stu-id="3d6da-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="3d6da-260">Para usar el método <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, llame a `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="3d6da-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="3d6da-261">El componente de vista se invoca en un Razor archivo (por ejemplo, *views/home/index. cshtml* ) con <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="3d6da-261">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml* ) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="3d6da-262">Llame a `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="3d6da-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="3d6da-263">Para usar el asistente de etiquetas, registre el ensamblado que contiene el componente de vista con el uso de la directiva `@addTagHelper` (el componente de vista se encuentra en un ensamblado denominado `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="3d6da-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="3d6da-264">Use la aplicación auxiliar de etiquetas de componente de vista en el Razor archivo de marcado:</span><span class="sxs-lookup"><span data-stu-id="3d6da-264">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="3d6da-265">La firma del método de `PriorityList.Invoke` es sincrónica, pero Razor busca y llama al método con `Component.InvokeAsync` en el archivo de marcado.</span><span class="sxs-lookup"><span data-stu-id="3d6da-265">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="3d6da-266">Todos los parámetros de componente de vista son obligatorios</span><span class="sxs-lookup"><span data-stu-id="3d6da-266">All view component parameters are required</span></span>

<span data-ttu-id="3d6da-267">Cada parámetro de un componente de vista es un atributo obligatorio.</span><span class="sxs-lookup"><span data-stu-id="3d6da-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="3d6da-268">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="3d6da-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="3d6da-269">Si se omite algún parámetro:</span><span class="sxs-lookup"><span data-stu-id="3d6da-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="3d6da-270">La firma del método `InvokeAsync` no coincidirá, por lo que el método no se ejecutará.</span><span class="sxs-lookup"><span data-stu-id="3d6da-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="3d6da-271">ViewComponent no representará ningún marcado.</span><span class="sxs-lookup"><span data-stu-id="3d6da-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="3d6da-272">No se producirá ningún error.</span><span class="sxs-lookup"><span data-stu-id="3d6da-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d6da-273">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3d6da-273">Additional resources</span></span>

* [<span data-ttu-id="3d6da-274">Inserción de dependencias en vistas</span><span class="sxs-lookup"><span data-stu-id="3d6da-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
