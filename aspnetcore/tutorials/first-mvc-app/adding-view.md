---
title: Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: d6a541e9633d3ecc2768744956c3b43d830b0b2b
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213119"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="47aac-103">Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="47aac-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="47aac-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="47aac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47aac-105">En esta sección, se modificará la clase `HelloWorldController` para usar los archivos de vista de [Razor](xref:mvc/views/razor) con el objetivo de encapsular correctamente el proceso de generar respuestas HTML a un cliente.</span><span class="sxs-lookup"><span data-stu-id="47aac-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="47aac-106">Para crear un archivo de plantilla de vista se usa Razor.</span><span class="sxs-lookup"><span data-stu-id="47aac-106">You create a view template file using Razor.</span></span> <span data-ttu-id="47aac-107">Las plantillas de vista basadas en Razor tienen una extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="47aac-108">Ofrecen una forma elegante de crear un resultado HTML con C#.</span><span class="sxs-lookup"><span data-stu-id="47aac-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="47aac-109">Actualmente, el método `Index` devuelve una cadena con un mensaje que está codificado de forma rígida en la clase de controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="47aac-110">En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="47aac-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="47aac-111">El código anterior llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="47aac-112">Este usa una plantilla de vista para generar una respuesta HTML.</span><span class="sxs-lookup"><span data-stu-id="47aac-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="47aac-113">Los métodos de controlador (también conocidos como *métodos de acción*), como el método `Index` anterior, suelen devolver un valor <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, en lugar de un tipo como una cadena `string`.</span><span class="sxs-lookup"><span data-stu-id="47aac-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="47aac-114">Agregar una vista</span><span class="sxs-lookup"><span data-stu-id="47aac-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47aac-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47aac-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47aac-116">Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="47aac-117">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="47aac-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="47aac-118">En el cuadro de diálogo **Agregar nuevo elemento - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="47aac-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="47aac-119">En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.</span><span class="sxs-lookup"><span data-stu-id="47aac-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="47aac-120">Seleccione **Vista de Razor** .</span><span class="sxs-lookup"><span data-stu-id="47aac-120">Select **Razor View**</span></span>

  * <span data-ttu-id="47aac-121">Conserve el valor del cuadro **Nombre**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="47aac-122">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="47aac-122">Select **Add**</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="47aac-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47aac-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="47aac-125">Agregue una vista `Index` para el `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="47aac-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="47aac-126">Agregue una nueva carpeta denominada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="47aac-127">Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47aac-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="47aac-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47aac-129">Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="47aac-130">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="47aac-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="47aac-131">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="47aac-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="47aac-132">Seleccione **ASP .NET Core** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="47aac-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="47aac-133">Seleccione **Página de la vista de MVC** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="47aac-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="47aac-134">Escriba *Índice* en el cuadro **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="47aac-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="47aac-135">Seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="47aac-135">Select **New**.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="47aac-137">Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:</span><span class="sxs-lookup"><span data-stu-id="47aac-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="47aac-138">Navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="47aac-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="47aac-139">El método `Index` en `HelloWorldController` no hizo mucho; ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="47aac-140">Dado que no se especificó un nombre de archivo de plantilla de vista, MVC usa el archivo de vista predeterminado.</span><span class="sxs-lookup"><span data-stu-id="47aac-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="47aac-141">Este archivo tiene el mismo nombre que el método (`Index`), por lo que se usa la plantilla de vista en */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="47aac-142">La imagen siguiente muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista)</span><span class="sxs-lookup"><span data-stu-id="47aac-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="47aac-143">codificada de forma rígida en la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-143">hard-coded in the view.</span></span>

![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="47aac-145">Cambio de vistas y páginas de diseño</span><span class="sxs-lookup"><span data-stu-id="47aac-145">Change views and layout pages</span></span>

<span data-ttu-id="47aac-146">Seleccione los vínculos de menú (**MvcMovie** [Película de MVC], **Home** [Inicio] y **Privacy** [Privacidad]).</span><span class="sxs-lookup"><span data-stu-id="47aac-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="47aac-147">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="47aac-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="47aac-148">El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="47aac-149">Abra el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="47aac-150">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="47aac-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="47aac-151">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="47aac-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="47aac-152">`RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="47aac-153">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="47aac-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="47aac-154">Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="47aac-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="47aac-155">Reemplace el contenido del archivo *Views/Shared/_Layout.cshtml* por el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="47aac-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="47aac-156">Se resaltan los cambios:</span><span class="sxs-lookup"><span data-stu-id="47aac-156">The changes are highlighted:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="47aac-157">En el marcado anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="47aac-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="47aac-158">3 apariciones de `MvcMovie` a `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="47aac-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="47aac-159">El delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` a `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="47aac-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="47aac-160">En el marcado anterior, se omitieron el `asp-area=""` [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) y el valor de atributo porque esta aplicación no utiliza [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="47aac-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="47aac-161">**Nota**: El controlador `Movies` no se ha implementado.</span><span class="sxs-lookup"><span data-stu-id="47aac-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="47aac-162">En este momento, el vínculo `Movie App` no es funcional.</span><span class="sxs-lookup"><span data-stu-id="47aac-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="47aac-163">Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="47aac-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="47aac-164">Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):</span><span class="sxs-lookup"><span data-stu-id="47aac-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="47aac-166">Pulse el vínculo **Home** (Inicio) y observe que el texto del título y el delimitador también muestran **Movie App** (Aplicación de película).</span><span class="sxs-lookup"><span data-stu-id="47aac-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="47aac-167">Hemos realizado el cambio una vez en la plantilla de diseño y hemos conseguido que todas las páginas del sitio reflejen el nuevo texto de vínculo y el nuevo título.</span><span class="sxs-lookup"><span data-stu-id="47aac-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="47aac-168">Examine el archivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47aac-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="47aac-169">El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="47aac-170">Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="47aac-171">Cambie el título y el elemento `<h2>` del archivo de vista *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47aac-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="47aac-172">El título y el elemento `<h2>` son algo diferentes para que pueda ver qué parte del código cambia la presentación.</span><span class="sxs-lookup"><span data-stu-id="47aac-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="47aac-173">En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas).</span><span class="sxs-lookup"><span data-stu-id="47aac-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="47aac-174">La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:</span><span class="sxs-lookup"><span data-stu-id="47aac-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="47aac-175">Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="47aac-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="47aac-176">Tenga en cuenta que el título del explorador, el encabezado principal y los encabezados secundarios han cambiado.</span><span class="sxs-lookup"><span data-stu-id="47aac-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="47aac-177">(Si no ve los cambios en el explorador, es posible que esté viendo contenido almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="47aac-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="47aac-178">Presione Ctrl+F5 en el explorador para forzar que se cargue la respuesta del servidor). El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="47aac-179">El contenido de la plantilla de vista *Index.cshtml* se combina con la plantilla de vista *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="47aac-180">Se envía una única respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="47aac-181">Con las plantillas de diseño es fácil hacer cambios que se apliquen en todas las páginas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="47aac-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="47aac-182">Para obtener más información, vea [Diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="47aac-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="47aac-184">Nuestra pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista),</span><span class="sxs-lookup"><span data-stu-id="47aac-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="47aac-185">están codificados de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="47aac-185">message) is hard-coded, though.</span></span> <span data-ttu-id="47aac-186">La aplicación de MVC tiene una "V" (vista) y ha obtenido una "C" (controlador), pero todavía no tiene una "M" (modelo).</span><span class="sxs-lookup"><span data-stu-id="47aac-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="47aac-187">Pasar datos del controlador a la vista</span><span class="sxs-lookup"><span data-stu-id="47aac-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="47aac-188">Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante.</span><span class="sxs-lookup"><span data-stu-id="47aac-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="47aac-189">Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="47aac-190">El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="47aac-191">Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="47aac-192">Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta.</span><span class="sxs-lookup"><span data-stu-id="47aac-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="47aac-193">Procedimiento recomendado: las plantillas de vista **no** deben realizar lógica de negocios ni interactuar directamente con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="47aac-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="47aac-194">En su lugar, una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="47aac-195">Mantener esta "separación de intereses" ayuda a mantener el código limpio, fácil de probar y de mantener.</span><span class="sxs-lookup"><span data-stu-id="47aac-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="47aac-196">Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="47aac-197">En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="47aac-198">La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar las partes de datos adecuadas desde el controlador a la vista para que se genere la respuesta.</span><span class="sxs-lookup"><span data-stu-id="47aac-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="47aac-199">Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData` al que luego pueda obtener acceso la plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="47aac-200">En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="47aac-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="47aac-201">El diccionario `ViewData` es un objeto dinámico, lo que significa que puede utilizarse cualquier tipo; el objeto `ViewData` no tiene ninguna propiedad definida hasta que coloca algo dentro de él.</span><span class="sxs-lookup"><span data-stu-id="47aac-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="47aac-202">El [sistema de enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre (`name` y `numTimes`) de la cadena de consulta en la barra de dirección a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="47aac-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="47aac-203">El archivo *HelloWorldController.cs* completo tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="47aac-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="47aac-204">El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="47aac-205">Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="47aac-206">Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="47aac-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="47aac-207">Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="47aac-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="47aac-208">Guarde los cambios y vaya a esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="47aac-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="47aac-209">Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="47aac-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="47aac-210">El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="47aac-211">Después, la vista representa los datos como HTML en el explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-211">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="47aac-213">En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="47aac-214">Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="47aac-215">El enfoque del modelo de vista que consiste en pasar datos suele ser más preferible que el enfoque de diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="47aac-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="47aac-216">Consulte [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (Cuándo usar ViewBag, ViewData o TempData) para más información.</span><span class="sxs-lookup"><span data-stu-id="47aac-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="47aac-217">En el tutorial siguiente crearemos una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="47aac-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="47aac-218">[Anterior](adding-controller.md)
> [Siguiente](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="47aac-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47aac-219">En esta sección, se modificará la clase `HelloWorldController` para usar los archivos de vista de [Razor](xref:mvc/views/razor) con el objetivo de encapsular correctamente el proceso de generar respuestas HTML a un cliente.</span><span class="sxs-lookup"><span data-stu-id="47aac-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="47aac-220">Para crear un archivo de plantilla de vista se usa Razor.</span><span class="sxs-lookup"><span data-stu-id="47aac-220">You create a view template file using Razor.</span></span> <span data-ttu-id="47aac-221">Las plantillas de vista basadas en Razor tienen una extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="47aac-222">Ofrecen una forma elegante de crear un resultado HTML con C#.</span><span class="sxs-lookup"><span data-stu-id="47aac-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="47aac-223">Actualmente, el método `Index` devuelve una cadena con un mensaje que está codificado de forma rígida en la clase de controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="47aac-224">En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="47aac-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="47aac-225">El código anterior llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="47aac-226">Este usa una plantilla de vista para generar una respuesta HTML.</span><span class="sxs-lookup"><span data-stu-id="47aac-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="47aac-227">Los métodos de controlador (también conocidos como *métodos de acción*), como el método `Index` anterior, suelen devolver un valor <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, en lugar de un tipo como una cadena `string`.</span><span class="sxs-lookup"><span data-stu-id="47aac-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="47aac-228">Agregar una vista</span><span class="sxs-lookup"><span data-stu-id="47aac-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47aac-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47aac-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47aac-230">Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="47aac-231">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="47aac-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="47aac-232">En el cuadro de diálogo **Agregar nuevo elemento - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="47aac-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="47aac-233">En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.</span><span class="sxs-lookup"><span data-stu-id="47aac-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="47aac-234">Seleccione **Vista de Razor** .</span><span class="sxs-lookup"><span data-stu-id="47aac-234">Select **Razor View**</span></span>

  * <span data-ttu-id="47aac-235">Conserve el valor del cuadro **Nombre**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="47aac-236">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="47aac-236">Select **Add**</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="47aac-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47aac-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="47aac-239">Agregue una vista `Index` para el `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="47aac-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="47aac-240">Agregue una nueva carpeta denominada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="47aac-241">Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47aac-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="47aac-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47aac-243">Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="47aac-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="47aac-244">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="47aac-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="47aac-245">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="47aac-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="47aac-246">Seleccione **Web** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="47aac-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="47aac-247">Seleccione **Archivo HTML vacío** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="47aac-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="47aac-248">Escriba *Index.cshtml* en el cuadro **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="47aac-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="47aac-249">Seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="47aac-249">Select **New**.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="47aac-251">Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:</span><span class="sxs-lookup"><span data-stu-id="47aac-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="47aac-252">Navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="47aac-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="47aac-253">El método `Index` en `HelloWorldController` no hizo mucho; ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="47aac-254">Dado que no se especificó un nombre de archivo de plantilla de vista, MVC usa el archivo de vista predeterminado.</span><span class="sxs-lookup"><span data-stu-id="47aac-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="47aac-255">Este archivo tiene el mismo nombre que el método (`Index`), por lo que se usa en */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="47aac-256">La imagen siguiente muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista)</span><span class="sxs-lookup"><span data-stu-id="47aac-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="47aac-257">codificada de forma rígida en la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-257">hard-coded in the view.</span></span>

![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="47aac-259">Cambio de vistas y páginas de diseño</span><span class="sxs-lookup"><span data-stu-id="47aac-259">Change views and layout pages</span></span>

<span data-ttu-id="47aac-260">Seleccione los vínculos de menú (**MvcMovie** [Película de MVC], **Home** [Inicio] y **Privacy** [Privacidad]).</span><span class="sxs-lookup"><span data-stu-id="47aac-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="47aac-261">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="47aac-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="47aac-262">El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="47aac-263">Abra el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="47aac-264">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="47aac-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="47aac-265">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="47aac-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="47aac-266">`RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="47aac-267">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="47aac-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="47aac-268">Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="47aac-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="47aac-269">En los elementos de título y pie de página, cambie `MvcMovie` por `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="47aac-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="47aac-270">Cambie el delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` por `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="47aac-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="47aac-271">En el marcado siguiente se muestran los cambios:</span><span class="sxs-lookup"><span data-stu-id="47aac-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="47aac-272">En el marcado anterior, se omitió el `asp-area` [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) porque esta aplicación no utiliza [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="47aac-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="47aac-273">**Nota**: El controlador `Movies` no se ha implementado.</span><span class="sxs-lookup"><span data-stu-id="47aac-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="47aac-274">En este momento, el vínculo `Movie App` no es funcional.</span><span class="sxs-lookup"><span data-stu-id="47aac-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="47aac-275">Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="47aac-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="47aac-276">Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):</span><span class="sxs-lookup"><span data-stu-id="47aac-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="47aac-278">Pulse el vínculo **Home** (Inicio) y observe que el texto del título y el delimitador también muestran **Movie App** (Aplicación de película).</span><span class="sxs-lookup"><span data-stu-id="47aac-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="47aac-279">Hemos realizado el cambio una vez en la plantilla de diseño y hemos conseguido que todas las páginas del sitio reflejen el nuevo texto de vínculo y el nuevo título.</span><span class="sxs-lookup"><span data-stu-id="47aac-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="47aac-280">Examine el archivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47aac-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="47aac-281">El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="47aac-282">Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="47aac-283">Cambie el título y el elemento `<h2>` del archivo de vista *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47aac-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="47aac-284">El título y el elemento `<h2>` son algo diferentes para que pueda ver qué parte del código cambia la presentación.</span><span class="sxs-lookup"><span data-stu-id="47aac-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="47aac-285">En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas).</span><span class="sxs-lookup"><span data-stu-id="47aac-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="47aac-286">La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:</span><span class="sxs-lookup"><span data-stu-id="47aac-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="47aac-287">Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="47aac-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="47aac-288">Tenga en cuenta que el título del explorador, el encabezado principal y los encabezados secundarios han cambiado.</span><span class="sxs-lookup"><span data-stu-id="47aac-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="47aac-289">(Si no ve los cambios en el explorador, es posible que esté viendo contenido almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="47aac-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="47aac-290">Presione Ctrl+F5 en el explorador para forzar que se cargue la respuesta del servidor). El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="47aac-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="47aac-291">Observe también cómo el contenido de la plantilla de vista *Index.cshtml* se fusionó con la plantilla de vista *Views/Shared/_Layout.cshtml* y se envió una única respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="47aac-292">Con las plantillas de diseño es realmente fácil hacer cambios para que se apliquen en todas las páginas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="47aac-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="47aac-293">Para saber más, vea [Layout](xref:mvc/views/layout) (Diseño).</span><span class="sxs-lookup"><span data-stu-id="47aac-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="47aac-295">Nuestra pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista),</span><span class="sxs-lookup"><span data-stu-id="47aac-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="47aac-296">están codificados de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="47aac-296">message) is hard-coded, though.</span></span> <span data-ttu-id="47aac-297">La aplicación de MVC tiene una "V" (vista) y ha obtenido una "C" (controlador), pero todavía no tiene una "M" (modelo).</span><span class="sxs-lookup"><span data-stu-id="47aac-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="47aac-298">Pasar datos del controlador a la vista</span><span class="sxs-lookup"><span data-stu-id="47aac-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="47aac-299">Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante.</span><span class="sxs-lookup"><span data-stu-id="47aac-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="47aac-300">Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="47aac-301">El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="47aac-302">Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="47aac-303">Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta.</span><span class="sxs-lookup"><span data-stu-id="47aac-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="47aac-304">Procedimiento recomendado: las plantillas de vista **no** deben realizar lógica de negocios ni interactuar directamente con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="47aac-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="47aac-305">En su lugar, una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador.</span><span class="sxs-lookup"><span data-stu-id="47aac-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="47aac-306">Mantener esta "separación de intereses" ayuda a mantener el código limpio, fácil de probar y de mantener.</span><span class="sxs-lookup"><span data-stu-id="47aac-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="47aac-307">Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="47aac-308">En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="47aac-309">La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar las partes de datos adecuadas desde el controlador a la vista para que se genere la respuesta.</span><span class="sxs-lookup"><span data-stu-id="47aac-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="47aac-310">Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData` al que luego pueda obtener acceso la plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="47aac-311">En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="47aac-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="47aac-312">El diccionario `ViewData` es un objeto dinámico, lo que significa que puede utilizarse cualquier tipo; el objeto `ViewData` no tiene ninguna propiedad definida hasta que coloca algo dentro de él.</span><span class="sxs-lookup"><span data-stu-id="47aac-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="47aac-313">El [sistema de enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre (`name` y `numTimes`) de la cadena de consulta en la barra de dirección a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="47aac-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="47aac-314">El archivo *HelloWorldController.cs* completo tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="47aac-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="47aac-315">El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="47aac-316">Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47aac-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="47aac-317">Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="47aac-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="47aac-318">Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="47aac-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="47aac-319">Guarde los cambios y vaya a esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="47aac-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="47aac-320">Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="47aac-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="47aac-321">El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="47aac-322">Después, la vista representa los datos como HTML en el explorador.</span><span class="sxs-lookup"><span data-stu-id="47aac-322">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="47aac-324">En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="47aac-325">Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="47aac-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="47aac-326">El enfoque del modelo de vista que consiste en pasar datos suele ser más preferible que el enfoque de diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="47aac-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="47aac-327">Consulte [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (Cuándo usar ViewBag, ViewData o TempData) para más información.</span><span class="sxs-lookup"><span data-stu-id="47aac-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="47aac-328">En el tutorial siguiente crearemos una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="47aac-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="47aac-329">[Anterior](adding-controller.md)
> [Siguiente](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="47aac-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
