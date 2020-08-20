---
title: Vistas de ASP.NET Core MVC
author: ardalis
description: Obtenga información sobre la forma en que las vistas controlan la presentación de datos de la aplicación y la interacción del usuario en ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: mvc/views/overview
ms.openlocfilehash: 6afd69414f2dc0158f724c6e6f7b3a3e51c1e92c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630684"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="79a21-103">Vistas de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="79a21-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="79a21-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="79a21-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="79a21-105">En este documento se explican las vistas utilizadas en las aplicaciones de ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="79a21-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="79a21-106">Para obtener información sobre Razor las páginas, consulte [Introducción a Razor las páginas](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="79a21-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="79a21-107">En el patrón de controlador de vista de modelos (MVC), la *vista* se encarga de la presentación de los datos y de la interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="79a21-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="79a21-108">Una vista es una plantilla HTML con [ Razor marcado](xref:mvc/views/razor)incrustado.</span><span class="sxs-lookup"><span data-stu-id="79a21-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="79a21-109">Razor el marcado es código que interactúa con el marcado HTML para generar una página web que se envía al cliente.</span><span class="sxs-lookup"><span data-stu-id="79a21-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="79a21-110">En ASP.NET Core MVC, las vistas son archivos *. cshtml* que usan el [lenguaje de programación C#](/dotnet/csharp/) en el Razor marcado.</span><span class="sxs-lookup"><span data-stu-id="79a21-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="79a21-111">Por lo general, los archivos de vistas se agrupan en carpetas con el nombre de cada uno de los [controladores](xref:mvc/controllers/actions) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79a21-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="79a21-112">Las carpetas se almacenan en una carpeta llamada *Views* que está ubicada en la raíz de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="79a21-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Carpeta Views del Explorador de soluciones de Visual Studio abierta con la carpeta Home mostrando los archivos About.cshtml, Contact.cshtml y Index.cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="79a21-114">El controlador *Home* está representado por una carpeta *Home* situada dentro de la carpeta *Views*.</span><span class="sxs-lookup"><span data-stu-id="79a21-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="79a21-115">La carpeta *Home* contiene las vistas correspondientes a las páginas web *About*, *Contact* e *Index* (página principal).</span><span class="sxs-lookup"><span data-stu-id="79a21-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="79a21-116">Cuando un usuario solicita una de estas tres páginas web, las acciones del controlador *Home* determinan cuál de las tres vistas se usa para crear y devolver una página web al usuario.</span><span class="sxs-lookup"><span data-stu-id="79a21-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="79a21-117">Use [diseños](xref:mvc/views/layout) para cohesionar las secciones de la página web y reducir la repetición del código.</span><span class="sxs-lookup"><span data-stu-id="79a21-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="79a21-118">Los diseños suelen contener encabezado, elementos de menú y navegación y pie de página.</span><span class="sxs-lookup"><span data-stu-id="79a21-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="79a21-119">El encabezado y el pie de página suelen contener marcado reutilizable para muchos elementos de metadatos y vínculos a recursos de script y estilo.</span><span class="sxs-lookup"><span data-stu-id="79a21-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="79a21-120">Los diseños ayudan a evitar este marcado reutilizable en las vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="79a21-121">Las [vistas parciales](xref:mvc/views/partial) administran las partes reutilizables de las vistas para reducir la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="79a21-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="79a21-122">Por ejemplo, resulta útil usar una vista parcial en la biografía de un autor que aparece en varias vistas de un sitio web de blog.</span><span class="sxs-lookup"><span data-stu-id="79a21-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="79a21-123">Una biografía de autor es contenido de vista normal y no requiere que se ejecute código para generar el contenido de la página web.</span><span class="sxs-lookup"><span data-stu-id="79a21-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="79a21-124">El contenido de la biografía del autor está disponible para la vista simplemente mediante un enlace de modelos, por lo que resulta ideal usar una vista parcial para este tipo de contenido.</span><span class="sxs-lookup"><span data-stu-id="79a21-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="79a21-125">Los [componentes de la vista](xref:mvc/views/view-components) se asemejan a las vistas parciales en que permiten reducir el código repetitivo, pero son adecuados para ver contenido que requiere la ejecución de código en el servidor con el fin de representar la página web.</span><span class="sxs-lookup"><span data-stu-id="79a21-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="79a21-126">Los componentes de la vista son útiles cuando el contenido representado requiere una interacción con la base de datos, por ejemplo, en el carro de la compra de un sitio web.</span><span class="sxs-lookup"><span data-stu-id="79a21-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="79a21-127">Los componentes de la vista no se limitan a un enlace de modelos para generar la salida de la página web.</span><span class="sxs-lookup"><span data-stu-id="79a21-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="79a21-128">Ventajas de utilizar las vistas</span><span class="sxs-lookup"><span data-stu-id="79a21-128">Benefits of using views</span></span>

<span data-ttu-id="79a21-129">Las vistas separan el marcado de la interfaz de usuario de otras partes de la aplicación con el fin de ayudar a establecer una [separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) dentro de una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="79a21-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="79a21-130">Diseñar respetando el principio SoC hace que la aplicación sea modular, lo que ofrece varias ventajas:</span><span class="sxs-lookup"><span data-stu-id="79a21-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="79a21-131">La aplicación es más fácil de mantener, ya que está mejor organizada.</span><span class="sxs-lookup"><span data-stu-id="79a21-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="79a21-132">Las vistas generalmente se agrupan por característica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79a21-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="79a21-133">Esto facilita la búsqueda de vistas relacionadas cuando se trabaja en una característica.</span><span class="sxs-lookup"><span data-stu-id="79a21-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="79a21-134">Las partes de la aplicación están acopladas de forma ligera.</span><span class="sxs-lookup"><span data-stu-id="79a21-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="79a21-135">Las vistas de la aplicación se pueden compilar y actualizar por separado de los componentes de la lógica de negocios y el acceso a datos.</span><span class="sxs-lookup"><span data-stu-id="79a21-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="79a21-136">Las vistas de la aplicación se pueden modificar sin necesidad de tener que actualizar otras partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79a21-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="79a21-137">Es más fácil probar los elementos de la interfaz de usuario de la aplicación, ya que las vistas son unidades independientes.</span><span class="sxs-lookup"><span data-stu-id="79a21-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="79a21-138">Dada la mejora en la organización, es menos probable que se repitan secciones de la interfaz de usuario de forma accidental.</span><span class="sxs-lookup"><span data-stu-id="79a21-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="79a21-139">Creación de una vista</span><span class="sxs-lookup"><span data-stu-id="79a21-139">Creating a view</span></span>

<span data-ttu-id="79a21-140">Las vistas que son específicas de un controlador se crean en la carpeta *Views/[nombreDelControlador]*.</span><span class="sxs-lookup"><span data-stu-id="79a21-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="79a21-141">Las vistas compartidas entre controladores se colocan en la carpeta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="79a21-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="79a21-142">Para crear una vista, agregue un archivo nuevo y asígnele el mismo nombre que a la acción del controlador asociada con la extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a21-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="79a21-143">Para crear una vista que se corresponda con la acción *About* del controlador *Home*, cree un archivo *About.cshtml* en la carpeta *Views/Home*:</span><span class="sxs-lookup"><span data-stu-id="79a21-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="79a21-144">*Razor* el marcado comienza con el `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="79a21-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="79a21-145">Ejecute instrucciones de C# colocando código C# dentro de [ Razor bloques de código](xref:mvc/views/razor#razor-code-blocks) establecidos por llaves ( `{ ... }` ).</span><span class="sxs-lookup"><span data-stu-id="79a21-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="79a21-146">Por ejemplo, vea la asignación de "About" en `ViewData["Title"]` mostrada anteriormente.</span><span class="sxs-lookup"><span data-stu-id="79a21-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="79a21-147">Para mostrar valores en HTML, simplemente haga referencia al valor con el símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="79a21-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="79a21-148">Ver el contenido de los elementos `<h2>` y `<h3>` anteriores.</span><span class="sxs-lookup"><span data-stu-id="79a21-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="79a21-149">El contenido de la vista mostrado anteriormente es solo una parte de toda la página web que se presenta al usuario.</span><span class="sxs-lookup"><span data-stu-id="79a21-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="79a21-150">El resto del diseño de la página y otros aspectos comunes de la vista se especifican en otros archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="79a21-151">Para obtener más información, consulte el [tema Diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="79a21-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="79a21-152">Cómo especifican los controladores las vistas</span><span class="sxs-lookup"><span data-stu-id="79a21-152">How controllers specify views</span></span>

<span data-ttu-id="79a21-153">Las vistas normalmente las devuelven acciones como [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), que es un tipo de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="79a21-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="79a21-154">El método de acción puede crear y devolver `ViewResult` directamente, pero no es lo más común.</span><span class="sxs-lookup"><span data-stu-id="79a21-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="79a21-155">Puesto que la mayoría de los controladores heredan de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), simplemente se usa el método del asistente `View` para devolver `ViewResult`:</span><span class="sxs-lookup"><span data-stu-id="79a21-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="79a21-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="79a21-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="79a21-157">Cuando esta acción devuelve un resultado, la vista *About.cshtml* mostrada en la última sección se representa como la página web siguiente:</span><span class="sxs-lookup"><span data-stu-id="79a21-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Página Acerca de representada en el explorador Edge](overview/_static/about-page.png)

<span data-ttu-id="79a21-159">El método del asistente `View` tiene varias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="79a21-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="79a21-160">También puede especificar:</span><span class="sxs-lookup"><span data-stu-id="79a21-160">You can optionally specify:</span></span>

* <span data-ttu-id="79a21-161">Una vista explícita para devolver:</span><span class="sxs-lookup"><span data-stu-id="79a21-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="79a21-162">Un [modelo](xref:mvc/models/model-binding) para pasar a la vista:</span><span class="sxs-lookup"><span data-stu-id="79a21-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="79a21-163">Una vista y un modelo:</span><span class="sxs-lookup"><span data-stu-id="79a21-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="79a21-164">Detección de vista</span><span class="sxs-lookup"><span data-stu-id="79a21-164">View discovery</span></span>

<span data-ttu-id="79a21-165">Cuando una acción devuelve una vista, tiene lugar un proceso llamado *detección de vista*.</span><span class="sxs-lookup"><span data-stu-id="79a21-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="79a21-166">Este proceso determina qué archivo de vista se utiliza en función del nombre de la vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="79a21-167">El comportamiento predeterminado del método `View` (`return View();`) es devolver una vista con el mismo nombre que el método de acción desde el que se llama.</span><span class="sxs-lookup"><span data-stu-id="79a21-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="79a21-168">Por ejemplo, el nombre de método *About* `ActionResult` del controlador se usa para buscar un archivo de vista denominado *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a21-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="79a21-169">En primer lugar, el runtime busca la vista en la carpeta *Views/[nombreDelControlador]*.</span><span class="sxs-lookup"><span data-stu-id="79a21-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="79a21-170">Si no encuentra una vista que coincida, busca la vista en la carpeta *Shared*.</span><span class="sxs-lookup"><span data-stu-id="79a21-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="79a21-171">Da igual si se devuelve implícitamente `ViewResult` con `return View();` o si se pasa explícitamente el nombre de la vista al método `View` con `return View("<ViewName>");`.</span><span class="sxs-lookup"><span data-stu-id="79a21-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="79a21-172">En ambos casos, la detección de vista busca un archivo de vista coincidente en este orden:</span><span class="sxs-lookup"><span data-stu-id="79a21-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="79a21-173">*Views/\[nombreDeControlador]/\[nombreDeVista].cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a21-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="79a21-174">*Views/Shared/\[nombreDeVista].cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a21-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="79a21-175">En lugar del nombre de una vista, se puede proporcionar la ruta de acceso del archivo de vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="79a21-176">Si se utiliza una ruta de acceso absoluta que comience en la raíz de la aplicación (también puede empezar con "/" o "~/"), debe especificarse la extensión *.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79a21-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="79a21-177">También se puede usar una ruta de acceso relativa para especificar vistas de directorios distintos sin la extensión *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a21-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="79a21-178">Dentro de `HomeController`, se puede devolver la vista *Index* de las vistas *Manage* con una ruta de acceso relativa:</span><span class="sxs-lookup"><span data-stu-id="79a21-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="79a21-179">De forma similar, se puede indicar el directorio específico del controlador actual con el prefijo "./":</span><span class="sxs-lookup"><span data-stu-id="79a21-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="79a21-180">[Las vistas parciales](xref:mvc/views/partial) y los [componentes de vista](xref:mvc/views/view-components) usan mecanismos de detección similares (aunque no idénticos).</span><span class="sxs-lookup"><span data-stu-id="79a21-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="79a21-181">Puede personalizar la convención predeterminada para la localización de las vistas en la aplicación si utiliza una interfaz [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizada.</span><span class="sxs-lookup"><span data-stu-id="79a21-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="79a21-182">La detección de vistas se basa en la búsqueda de archivos de vista por nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="79a21-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="79a21-183">Si el sistema de archivos subyacente distingue mayúsculas de minúsculas, es probable que también lo hagan los nombres de las vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="79a21-184">Para que haya compatibilidad entre sistemas operativos, haga coincidir las letras mayúsculas y minúsculas de los nombres de controlador y acción con los nombres de archivo y carpetas de vista asociados.</span><span class="sxs-lookup"><span data-stu-id="79a21-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="79a21-185">Si mientras trabaja con un sistema de archivos que distingue mayúsculas de minúsculas se produce un error que le indica que no se encuentra un archivo de vista, confirme que el archivo de vista solicitado y el nombre de archivo de vista real coinciden en el uso de mayúsculas.</span><span class="sxs-lookup"><span data-stu-id="79a21-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="79a21-186">Siga el procedimiento recomendado de organizar la estructura de archivos de vistas de modo que refleje las relaciones existentes entre controladores, acciones y vistas para una mayor claridad y un mantenimiento más sencillo.</span><span class="sxs-lookup"><span data-stu-id="79a21-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="79a21-187">Paso de datos a las vistas</span><span class="sxs-lookup"><span data-stu-id="79a21-187">Passing data to views</span></span>

<span data-ttu-id="79a21-188">Se pueden pasar datos a vistas con varios métodos:</span><span class="sxs-lookup"><span data-stu-id="79a21-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="79a21-189">Datos fuertemente tipados: ViewModel</span><span class="sxs-lookup"><span data-stu-id="79a21-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="79a21-190">Datos débilmente tipados</span><span class="sxs-lookup"><span data-stu-id="79a21-190">Weakly typed data</span></span>
  * <span data-ttu-id="79a21-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="79a21-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="79a21-192">Datos fuertemente tipados (ViewModel)</span><span class="sxs-lookup"><span data-stu-id="79a21-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="79a21-193">El enfoque más eficaz consiste en especificar un tipo de [modelo](xref:mvc/models/model-binding) en la vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="79a21-194">Este modelo se conoce normalmente como *viewmodel* (modelo de vista)</span><span class="sxs-lookup"><span data-stu-id="79a21-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="79a21-195">y en él se pasa una instancia de tipo viewmodel a la vista de la acción.</span><span class="sxs-lookup"><span data-stu-id="79a21-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="79a21-196">La utilización de un modelo de vista para pasar datos a una vista permite que la vista se beneficie de las ventajas de la comprobación de tipos *seguros*.</span><span class="sxs-lookup"><span data-stu-id="79a21-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="79a21-197">El término *establecimiento fuerte de tipos* (o *fuertemente tipado*) significa que cada variable y constante tienen un tipo definido explícitamente, por ejemplo, `string`, `int` o `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="79a21-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="79a21-198">La validez de los tipos usados en una vista se comprueba en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="79a21-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="79a21-199">[Visual Studio](https://visualstudio.microsoft.com) y [Visual Studio Code](https://code.visualstudio.com/) enumeran los miembros de clase fuertemente tipados mediante una característica denominada [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="79a21-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="79a21-200">Si quiere ver las propiedades de un modelo de vista, escriba el nombre de variable del modelo de vista seguido por un punto (`.`).</span><span class="sxs-lookup"><span data-stu-id="79a21-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="79a21-201">Esto ayuda a escribir código más rápidamente y con menos errores.</span><span class="sxs-lookup"><span data-stu-id="79a21-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="79a21-202">Especifique un modelo con la directiva `@model`.</span><span class="sxs-lookup"><span data-stu-id="79a21-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="79a21-203">Utilice el modelo con `@Model`:</span><span class="sxs-lookup"><span data-stu-id="79a21-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="79a21-204">Para proporcionar el modelo a la vista, el controlador lo pasa como un parámetro:</span><span class="sxs-lookup"><span data-stu-id="79a21-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="79a21-205">No hay ninguna restricción sobre los tipos de modelo que se pueden proporcionar a una vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="79a21-206">Se recomienda usar modelos de vista POCO (objeto CRL estándar) con poco o ningún comportamiento (métodos) definido.</span><span class="sxs-lookup"><span data-stu-id="79a21-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="79a21-207">Por lo general, las clases de modelo de vista se almacenan en la carpeta *Models* o en otra carpeta *ViewModels* en la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79a21-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="79a21-208">El modelo de vista *Address* usado en el ejemplo anterior es un modelo de vista POCO almacenado en un archivo denominado *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="79a21-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="79a21-209">Nada le impide usar las mismas clases tanto para los tipos de modelo de vista como para los de modelo de negocio.</span><span class="sxs-lookup"><span data-stu-id="79a21-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="79a21-210">Pero el uso de modelos independientes permite que las vistas varíen de forma independiente de las partes de lógica de negocios y acceso de datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79a21-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="79a21-211">La separación de los modelos y los modelos de vista también ofrece ventajas de seguridad cuando los modelos utilizan [enlace de modelo](xref:mvc/models/model-binding) y [validación](xref:mvc/models/validation) para los datos enviados a la aplicación por el usuario.</span><span class="sxs-lookup"><span data-stu-id="79a21-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="79a21-212">Datos débilmente tipados (ViewData, atributo ViewData y ViewBag)</span><span class="sxs-lookup"><span data-stu-id="79a21-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="79a21-213">`ViewBag`*no está disponible en Razor Páginas.*</span><span class="sxs-lookup"><span data-stu-id="79a21-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="79a21-214">Además de las vistas fuertemente tipadas, las vistas tienen acceso a una colección de datos *débilmente tipados*, también denominados *imprecisos*.</span><span class="sxs-lookup"><span data-stu-id="79a21-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="79a21-215">A diferencia de los tipos fuertes, en los *tipos débiles* (o *débilmente tipados*) no se declara explícitamente el tipo de datos que se está utilizando.</span><span class="sxs-lookup"><span data-stu-id="79a21-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="79a21-216">Puede usar la colección de datos débilmente tipados para pasar pequeñas cantidades de datos de los controladores y las vistas, tanto en dirección de entrada como de salida.</span><span class="sxs-lookup"><span data-stu-id="79a21-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="79a21-217">Pasar datos entre...</span><span class="sxs-lookup"><span data-stu-id="79a21-217">Passing data between a ...</span></span>                        | <span data-ttu-id="79a21-218">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="79a21-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="79a21-219">Un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="79a21-219">Controller and a view</span></span>                             | <span data-ttu-id="79a21-220">Rellenar una lista desplegable con datos.</span><span class="sxs-lookup"><span data-stu-id="79a21-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="79a21-221">Una vista y una [vista de diseño](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="79a21-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="79a21-222">Establecer el **\<title>** contenido del elemento en la vista de diseño desde un archivo de vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="79a21-223">Una [vista parcial](xref:mvc/views/partial) y una vista</span><span class="sxs-lookup"><span data-stu-id="79a21-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="79a21-224">Un widget que muestra datos basados en la página web que el usuario solicitó.</span><span class="sxs-lookup"><span data-stu-id="79a21-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="79a21-225">Puede hacer referencia a esta colección a través de las propiedades `ViewData` o `ViewBag` en controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="79a21-226">La propiedad `ViewData` es un diccionario de objetos débilmente tipados.</span><span class="sxs-lookup"><span data-stu-id="79a21-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="79a21-227">La propiedad `ViewBag` es un contenedor alrededor de `ViewData` que proporciona propiedades dinámicas para la colección `ViewData` subyacente.</span><span class="sxs-lookup"><span data-stu-id="79a21-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="79a21-228">Nota: las búsquedas de clave no distinguen mayúsculas de minúsculas para `ViewData` y `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="79a21-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="79a21-229">`ViewData` y `ViewBag` se resuelven de forma dinámica en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="79a21-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="79a21-230">Debido a que no ofrecen la comprobación de tipos en tiempo de compilación, ambas son generalmente más propensas a errores que el uso de un modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="79a21-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="79a21-231">Por esta razón, algunos desarrolladores prefieren prescindir de `ViewData` y `ViewBag` o usarlos lo menos posible.</span><span class="sxs-lookup"><span data-stu-id="79a21-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="79a21-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="79a21-232">**ViewData**</span></span>

<span data-ttu-id="79a21-233">`ViewData` es un objeto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) al que se accede a través de claves `string`.</span><span class="sxs-lookup"><span data-stu-id="79a21-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="79a21-234">Los datos de cadena se pueden almacenar y utilizar directamente sin necesidad de una conversión, pero primero debe convertir otros valores de objeto `ViewData` a tipos específicos cuando los extrae.</span><span class="sxs-lookup"><span data-stu-id="79a21-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="79a21-235">Se puede usar `ViewData` para pasar datos de los controladores a las vistas y dentro de las vistas, incluidas las [vistas parciales](xref:mvc/views/partial) y los [diseños](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="79a21-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="79a21-236">En el ejemplo siguiente se usa `ViewData` en una acción para establecer los valores de saludo y dirección:</span><span class="sxs-lookup"><span data-stu-id="79a21-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="79a21-237">Trabajar con los datos en una vista:</span><span class="sxs-lookup"><span data-stu-id="79a21-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="79a21-238">**Atributo ViewData**</span><span class="sxs-lookup"><span data-stu-id="79a21-238">**ViewData attribute**</span></span>

<span data-ttu-id="79a21-239">Otro método en el que se usa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) es [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="79a21-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="79a21-240">Las propiedades de los controladores o Razor modelos de página marcados con el `[ViewData]` atributo tienen sus valores almacenados y cargados desde el diccionario.</span><span class="sxs-lookup"><span data-stu-id="79a21-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="79a21-241">En el siguiente ejemplo, el controlador Home contiene una propiedad `Title` marcada con `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="79a21-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="79a21-242">El método `About` establece el título de la vista About:</span><span class="sxs-lookup"><span data-stu-id="79a21-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="79a21-243">En el diseño, el título se lee desde el diccionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="79a21-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="79a21-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="79a21-244">**ViewBag**</span></span>

<span data-ttu-id="79a21-245">`ViewBag`*no está disponible en Razor Páginas.*</span><span class="sxs-lookup"><span data-stu-id="79a21-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="79a21-246">`ViewBag` es un objeto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) que proporciona acceso dinámico a los objetos almacenados en `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79a21-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="79a21-247">`ViewBag` puede ser más cómodo de trabajar con él, ya que no requiere conversión.</span><span class="sxs-lookup"><span data-stu-id="79a21-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="79a21-248">En el ejemplo siguiente se muestra cómo usar `ViewBag` con el mismo resultado que al usar `ViewData` anteriormente:</span><span class="sxs-lookup"><span data-stu-id="79a21-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="79a21-249">**Uso simultáneo de ViewData y ViewBag**</span><span class="sxs-lookup"><span data-stu-id="79a21-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="79a21-250">`ViewBag`*no está disponible en Razor Páginas.*</span><span class="sxs-lookup"><span data-stu-id="79a21-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="79a21-251">Puesto que `ViewData` y `ViewBag` hacen referencia a la misma colección `ViewData` subyacente, se pueden utilizar `ViewData` y `ViewBag`, y combinarlos entre ellos al leer y escribir valores.</span><span class="sxs-lookup"><span data-stu-id="79a21-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="79a21-252">Establezca el título con `ViewBag` y la descripción con `ViewData` en la parte superior de una vista *About.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79a21-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="79a21-253">Lea las propiedades pero invierta el uso de `ViewData` y `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="79a21-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="79a21-254">En el archivo *_Layout.cshtml*, obtenga el título con `ViewData` y la descripción con `ViewBag`:</span><span class="sxs-lookup"><span data-stu-id="79a21-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="79a21-255">Recuerde que las cadenas no requieren una conversión para `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79a21-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="79a21-256">Puede usar `@ViewData["Title"]` sin conversión alguna.</span><span class="sxs-lookup"><span data-stu-id="79a21-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="79a21-257">Es posible utilizar `ViewData` y `ViewBag` al mismo tiempo, al igual que combinar la lectura y escritura de propiedades.</span><span class="sxs-lookup"><span data-stu-id="79a21-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="79a21-258">Se representa el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="79a21-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="79a21-259">**Resumen de las diferencias entre ViewData y ViewBag**</span><span class="sxs-lookup"><span data-stu-id="79a21-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="79a21-260">`ViewBag` no está disponible en las Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="79a21-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="79a21-261">Se deriva de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), por lo que tiene propiedades de diccionario que pueden ser útiles, como `ContainsKey` ,, `Add` `Remove` y `Clear` .</span><span class="sxs-lookup"><span data-stu-id="79a21-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="79a21-262">Las claves del diccionario son cadenas, por lo que se permiten espacios en blanco.</span><span class="sxs-lookup"><span data-stu-id="79a21-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="79a21-263">Ejemplo: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="79a21-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="79a21-264">Todos los tipos excepto `string` deben convertirse en la vista que usa `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79a21-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="79a21-265">Se deriva de [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), por lo que permite la creación de propiedades dinámicas mediante la notación de puntos (`@ViewBag.SomeKey = <value or object>`), y no se requiere ninguna conversión.</span><span class="sxs-lookup"><span data-stu-id="79a21-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="79a21-266">La sintaxis de `ViewBag` hace que sea más rápido de agregar a controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="79a21-267">Es más sencillo comprobar si hay valores null.</span><span class="sxs-lookup"><span data-stu-id="79a21-267">Simpler to check for null values.</span></span> <span data-ttu-id="79a21-268">Ejemplo: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="79a21-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="79a21-269">**Cuándo utilizar ViewData o ViewBag**</span><span class="sxs-lookup"><span data-stu-id="79a21-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="79a21-270">`ViewData` y `ViewBag` son dos enfoques igualmente válidos para pasar pequeñas cantidades de datos entre controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="79a21-271">La elección de cuál utilizar se basa en la preferencia.</span><span class="sxs-lookup"><span data-stu-id="79a21-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="79a21-272">Aunque se pueden combinar objetos `ViewData` y `ViewBag`, el código resulta más fácil de leer y mantener si se utiliza un único enfoque de forma sistemática.</span><span class="sxs-lookup"><span data-stu-id="79a21-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="79a21-273">Ambos enfoques se resuelven dinámicamente en tiempo de ejecución y, por tanto, son propensos a generar errores en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="79a21-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="79a21-274">Algunos equipos de desarrollo los evitan.</span><span class="sxs-lookup"><span data-stu-id="79a21-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="79a21-275">Vistas dinámicas</span><span class="sxs-lookup"><span data-stu-id="79a21-275">Dynamic views</span></span>

<span data-ttu-id="79a21-276">Las vistas que no declaran un tipo modelo con `@model`, pero que tienen una instancia de modelo que se les pasa (por ejemplo, `return View(Address);`), pueden hacer referencia a las propiedades de la instancia dinámicamente:</span><span class="sxs-lookup"><span data-stu-id="79a21-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="79a21-277">Esta característica proporciona la flexibilidad, pero no ofrece protección de compilación ni IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="79a21-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="79a21-278">Si la propiedad no existe, se produce un error en la generación de la página web en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="79a21-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="79a21-279">Más características de vista</span><span class="sxs-lookup"><span data-stu-id="79a21-279">More view features</span></span>

<span data-ttu-id="79a21-280">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten agregar fácilmente el comportamiento del lado servidor a las etiquetas HTML existentes.</span><span class="sxs-lookup"><span data-stu-id="79a21-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="79a21-281">El uso de asistentes de etiquetas evita la necesidad de escribir código personalizado o asistentes en las vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="79a21-282">Los asistentes de etiquetas se aplican como atributos a elementos HTML y son ignoradas por los editores que no pueden procesarlas.</span><span class="sxs-lookup"><span data-stu-id="79a21-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="79a21-283">Esto permite editar y representar el marcado de la vista con varias herramientas.</span><span class="sxs-lookup"><span data-stu-id="79a21-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="79a21-284">Hay muchos asistentes de HTML integradas que permiten generar marcado HTML personalizado.</span><span class="sxs-lookup"><span data-stu-id="79a21-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="79a21-285">La lógica más compleja de la interfaz de usuario se puede administrar mediante [componentes de vista](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="79a21-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="79a21-286">Los componentes de vista proporcionan la misma SoC que los controladores y las vistas.</span><span class="sxs-lookup"><span data-stu-id="79a21-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="79a21-287">En este sentido, pueden eliminar la necesidad de acciones y vistas que se encargan de los datos utilizados por elementos comunes de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="79a21-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="79a21-288">Al igual que muchos otros aspectos de ASP.NET Core, las vistas admiten [inserción de dependencias](xref:fundamentals/dependency-injection), lo que permite que los servicios se puedan [insertar en las vistas](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="79a21-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
