---
title: Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804615"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="79795-103">Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="79795-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="79795-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="79795-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79795-105">En esta sección, modificará la clase `HelloWorldController` para usar archivos de vista de [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="79795-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="79795-106">Esto encapsula correctamente el proceso de generar respuestas HTML a un cliente.</span><span class="sxs-lookup"><span data-stu-id="79795-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="79795-107">Las plantillas de vista se crean mediante Razor.</span><span class="sxs-lookup"><span data-stu-id="79795-107">View templates are created using Razor.</span></span> <span data-ttu-id="79795-108">Las plantillas de vista basadas en Razor:</span><span class="sxs-lookup"><span data-stu-id="79795-108">Razor-based view templates:</span></span>

* <span data-ttu-id="79795-109">Tienen una extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="79795-110">Ofrecen una forma elegante de crear un resultado HTML con C#.</span><span class="sxs-lookup"><span data-stu-id="79795-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="79795-111">Actualmente, el método `Index` devuelve una cadena con un mensaje en la clase de controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="79795-112">En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="79795-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="79795-113">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="79795-113">The preceding code:</span></span>

* <span data-ttu-id="79795-114">Llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="79795-115">Usa una plantilla de vista para generar una respuesta HTML.</span><span class="sxs-lookup"><span data-stu-id="79795-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="79795-116">Los métodos de controlador:</span><span class="sxs-lookup"><span data-stu-id="79795-116">Controller methods:</span></span>

* <span data-ttu-id="79795-117">Reciben el nombre de *métodos de acción*.</span><span class="sxs-lookup"><span data-stu-id="79795-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="79795-118">Por ejemplo, el método de acción `Index` del código anterior.</span><span class="sxs-lookup"><span data-stu-id="79795-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="79795-119">Normalmente devuelven un elemento <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, no un tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="79795-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="79795-120">Agregar una vista</span><span class="sxs-lookup"><span data-stu-id="79795-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79795-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79795-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="79795-122">Haga clic con el botón derecho en la carpeta *Views*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="79795-123">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="79795-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="79795-124">En el cuadro de diálogo **Add New Item - MvcMovie** (Agregar nuevo elemento: MvcMovie):</span><span class="sxs-lookup"><span data-stu-id="79795-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="79795-125">En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.</span><span class="sxs-lookup"><span data-stu-id="79795-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="79795-126">Seleccione **Vista de Razor** .</span><span class="sxs-lookup"><span data-stu-id="79795-126">Select **Razor View**</span></span>
* <span data-ttu-id="79795-127">Conserve el valor del cuadro **Nombre**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="79795-128">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="79795-128">Select **Add**</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="79795-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="79795-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="79795-131">Agregue una vista `Index` para `HelloWorldController`:</span><span class="sxs-lookup"><span data-stu-id="79795-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="79795-132">Agregue una nueva carpeta denominada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="79795-133">Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="79795-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="79795-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="79795-135">Mientras mantiene presionado CTRL, haga clic en la carpeta *Views*, en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="79795-136">Mientras mantiene presionado CTRL, haga clic en la carpeta *Views/HelloWorld* y en **Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="79795-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="79795-137">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="79795-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="79795-138">Seleccione **ASP.NET Core** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="79795-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="79795-139">Seleccione **Vista de Razor: vacía** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="79795-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="79795-140">Escriba *Índice* en el cuadro **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="79795-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="79795-141">Seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="79795-141">Select **New**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="79795-143">Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:</span><span class="sxs-lookup"><span data-stu-id="79795-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="79795-144">Vaya a `https://localhost:{PORT}/HelloWorld`:</span><span class="sxs-lookup"><span data-stu-id="79795-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="79795-145">El método `Index` en `HelloWorldController` ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="79795-146">No se especificó un nombre de archivo de plantilla de vista, por lo que MVC usa el archivo de vista predeterminado.</span><span class="sxs-lookup"><span data-stu-id="79795-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="79795-147">Cuando no se especifica el nombre del archivo de vista, se devuelve la vista predeterminada.</span><span class="sxs-lookup"><span data-stu-id="79795-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="79795-148">En este ejemplo, la vista predeterminada tiene el mismo nombre que el método de acción (`Index`).</span><span class="sxs-lookup"><span data-stu-id="79795-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="79795-149">Se usa la plantilla de vista */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="79795-150">En la imagen siguiente se muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista)</span><span class="sxs-lookup"><span data-stu-id="79795-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="79795-151">codificada de forma rígida en la vista:</span><span class="sxs-lookup"><span data-stu-id="79795-151">hard-coded in the view:</span></span>

  ![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="79795-153">Cambio de vistas y páginas de diseño</span><span class="sxs-lookup"><span data-stu-id="79795-153">Change views and layout pages</span></span>

<span data-ttu-id="79795-154">Seleccione los vínculos de menú **MvcMovie** (Película de MVC), **Home** (Inicio) y **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="79795-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="79795-155">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="79795-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="79795-156">El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="79795-157">Abra el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="79795-158">Las plantillas de [diseño](xref:mvc/views/layout) permiten:</span><span class="sxs-lookup"><span data-stu-id="79795-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="79795-159">Especificar el diseño del contenedor HTML de un sitio en un solo lugar</span><span class="sxs-lookup"><span data-stu-id="79795-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="79795-160">Aplicar el diseño del contenedor HTML en varias páginas del sitio</span><span class="sxs-lookup"><span data-stu-id="79795-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="79795-161">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="79795-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="79795-162">`RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="79795-163">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="79795-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="79795-164">Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="79795-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="79795-165">Reemplace el contenido del archivo *Views/Shared/_Layout.cshtml* por el marcado siguiente.</span><span class="sxs-lookup"><span data-stu-id="79795-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="79795-166">Se resaltan los cambios:</span><span class="sxs-lookup"><span data-stu-id="79795-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79795-167">En el marcado anterior se realizan los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="79795-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="79795-168">Tres apariciones de `MvcMovie` a `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="79795-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="79795-169">El delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` a `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="79795-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="79795-170">En el marcado anterior, se omitieron el [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) y el valor de atributo de `asp-area=""` porque esta aplicación no usa [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="79795-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="79795-171">**Nota**: El controlador `Movies` no se ha implementado.</span><span class="sxs-lookup"><span data-stu-id="79795-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="79795-172">En este momento, el vínculo `Movie App` no es funcional.</span><span class="sxs-lookup"><span data-stu-id="79795-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="79795-173">Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="79795-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="79795-174">Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):</span><span class="sxs-lookup"><span data-stu-id="79795-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="79795-176">Haga clic en el vínculo **Página principal**.</span><span class="sxs-lookup"><span data-stu-id="79795-176">Select the **Home** link.</span></span>

<span data-ttu-id="79795-177">Observe que en el texto del título y del delimitador aparece **Movie App** (Aplicación de película).</span><span class="sxs-lookup"><span data-stu-id="79795-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="79795-178">Los cambios se realizaron una vez en la plantilla de diseño, y todas las páginas del sitio reflejan el nuevo texto de vínculo y el nuevo título.</span><span class="sxs-lookup"><span data-stu-id="79795-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="79795-179">Examine el archivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79795-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="79795-180">El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista.</span><span class="sxs-lookup"><span data-stu-id="79795-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="79795-181">Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="79795-182">Abra el archivo de vista *Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="79795-183">Cambie el título y el elemento `<h2>` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="79795-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="79795-184">El título y el elemento `<h2>` son algo diferentes para que quede claro qué parte del código cambia la presentación.</span><span class="sxs-lookup"><span data-stu-id="79795-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="79795-185">En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas).</span><span class="sxs-lookup"><span data-stu-id="79795-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="79795-186">La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:</span><span class="sxs-lookup"><span data-stu-id="79795-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="79795-187">Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="79795-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="79795-188">Tenga en cuenta que los siguientes elementos han cambiado:</span><span class="sxs-lookup"><span data-stu-id="79795-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="79795-189">Título del explorador</span><span class="sxs-lookup"><span data-stu-id="79795-189">Browser title.</span></span>
* <span data-ttu-id="79795-190">Encabezado principal</span><span class="sxs-lookup"><span data-stu-id="79795-190">Primary heading.</span></span>
* <span data-ttu-id="79795-191">Encabezados secundarios</span><span class="sxs-lookup"><span data-stu-id="79795-191">Secondary headings.</span></span>

<span data-ttu-id="79795-192">Si no hay ningún cambio en el explorador, podría estar viéndose contenido almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="79795-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="79795-193">Presione CTRL+F5 en el explorador para forzar que se cargue la respuesta del servidor.</span><span class="sxs-lookup"><span data-stu-id="79795-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="79795-194">El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="79795-195">El contenido de la plantilla de vista *Index.cshtml* se combina con la plantilla de vista *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="79795-196">Se envía una única respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="79795-197">Con las plantillas de diseño es fácil hacer cambios que se apliquen en todas las páginas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="79795-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="79795-198">Para obtener más información, vea [Diseño](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="79795-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="79795-200">La pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista),</span><span class="sxs-lookup"><span data-stu-id="79795-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="79795-201">está codificada de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="79795-201">message, is hard-coded however.</span></span> <span data-ttu-id="79795-202">La aplicación de MVC tiene una "V" (vista) y una "C" (controlador), pero todavía no tiene una "M" (modelo).</span><span class="sxs-lookup"><span data-stu-id="79795-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="79795-203">Pasar datos del controlador a la vista</span><span class="sxs-lookup"><span data-stu-id="79795-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="79795-204">Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante.</span><span class="sxs-lookup"><span data-stu-id="79795-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="79795-205">Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="79795-206">El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="79795-207">Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="79795-208">Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta.</span><span class="sxs-lookup"><span data-stu-id="79795-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="79795-209">Las plantillas de vista **no** deben:</span><span class="sxs-lookup"><span data-stu-id="79795-209">View templates should **not**:</span></span>

* <span data-ttu-id="79795-210">Hacer lógica de negocios</span><span class="sxs-lookup"><span data-stu-id="79795-210">Do business logic</span></span>
* <span data-ttu-id="79795-211">Interactuar con una base de datos directamente</span><span class="sxs-lookup"><span data-stu-id="79795-211">Interact with a database directly.</span></span>

<span data-ttu-id="79795-212">Una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="79795-213">Mantener esta "separación de intereses" ayuda a mantener el código:</span><span class="sxs-lookup"><span data-stu-id="79795-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="79795-214">Limpio</span><span class="sxs-lookup"><span data-stu-id="79795-214">Clean.</span></span>
* <span data-ttu-id="79795-215">Fácil de probar</span><span class="sxs-lookup"><span data-stu-id="79795-215">Testable.</span></span>
* <span data-ttu-id="79795-216">Fácil de mantener</span><span class="sxs-lookup"><span data-stu-id="79795-216">Maintainable.</span></span>

<span data-ttu-id="79795-217">Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="79795-218">En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="79795-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="79795-219">La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar los datos adecuados del controlador a la vista para que se genere la respuesta.</span><span class="sxs-lookup"><span data-stu-id="79795-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="79795-220">Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79795-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="79795-221">Después, la plantilla de vista podrá acceder a los datos dinámicos.</span><span class="sxs-lookup"><span data-stu-id="79795-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="79795-222">En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79795-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="79795-223">El diccionario `ViewData` es un objeto dinámico, lo que significa que se puede usar cualquier tipo.</span><span class="sxs-lookup"><span data-stu-id="79795-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="79795-224">El objeto `ViewData` no tiene ninguna propiedad definida hasta que se agrega algo.</span><span class="sxs-lookup"><span data-stu-id="79795-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="79795-225">El [sistema de enlace de modelos de MVC](xref:mvc/models/model-binding) asigna automáticamente los parámetros con nombre `name` y `numTimes` de la cadena de consulta a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="79795-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="79795-226">`HelloWorldController` completo:</span><span class="sxs-lookup"><span data-stu-id="79795-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="79795-227">El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.</span><span class="sxs-lookup"><span data-stu-id="79795-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="79795-228">Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="79795-229">Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="79795-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="79795-230">Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="79795-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="79795-231">Guarde los cambios y vaya a esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="79795-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="79795-232">Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos de MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="79795-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="79795-233">El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista.</span><span class="sxs-lookup"><span data-stu-id="79795-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="79795-234">Después, la vista representa los datos como HTML en el explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-234">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="79795-236">En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="79795-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="79795-237">Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="79795-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="79795-238">El enfoque del modelo de vista que consiste en pasar datos es preferible al enfoque de diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79795-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="79795-239">En el tutorial siguiente crearemos una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="79795-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="79795-240">[Anterior: Adición de un controlador](adding-controller.md)
> [Siguiente: Adición de un modelo](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="79795-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79795-241">En esta sección, se modifica la clase `HelloWorldController` para usar los archivos de vista de [Razor](xref:mvc/views/razor) con el objetivo de encapsular correctamente el proceso de generar respuestas HTML a un cliente.</span><span class="sxs-lookup"><span data-stu-id="79795-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="79795-242">Para crear un archivo de plantilla de vista se usa Razor.</span><span class="sxs-lookup"><span data-stu-id="79795-242">You create a view template file using Razor.</span></span> <span data-ttu-id="79795-243">Las plantillas de vista basadas en Razor tienen una extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="79795-244">Ofrecen una forma elegante de crear un resultado HTML con C#.</span><span class="sxs-lookup"><span data-stu-id="79795-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="79795-245">Actualmente, el método `Index` devuelve una cadena con un mensaje que está codificado de forma rígida en la clase de controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="79795-246">En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="79795-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="79795-247">El código anterior llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="79795-248">Este usa una plantilla de vista para generar una respuesta HTML.</span><span class="sxs-lookup"><span data-stu-id="79795-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="79795-249">Los métodos de controlador (también conocidos como *métodos de acción*), como el método `Index` anterior, suelen devolver un valor <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, en lugar de un tipo como una cadena `string`.</span><span class="sxs-lookup"><span data-stu-id="79795-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="79795-250">Agregar una vista</span><span class="sxs-lookup"><span data-stu-id="79795-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79795-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79795-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="79795-252">Haga clic con el botón derecho en la carpeta *Views*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="79795-253">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="79795-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="79795-254">En el cuadro de diálogo **Agregar nuevo elemento - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="79795-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="79795-255">En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.</span><span class="sxs-lookup"><span data-stu-id="79795-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="79795-256">Seleccione **Vista de Razor** .</span><span class="sxs-lookup"><span data-stu-id="79795-256">Select **Razor View**</span></span>

  * <span data-ttu-id="79795-257">Conserve el valor del cuadro **Nombre**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="79795-258">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="79795-258">Select **Add**</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="79795-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="79795-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="79795-261">Agregue una vista `Index` para el `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="79795-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="79795-262">Agregue una nueva carpeta denominada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="79795-263">Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="79795-264">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="79795-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="79795-265">Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="79795-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="79795-266">Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="79795-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="79795-267">En el cuadro de diálogo **Nuevo archivo**:</span><span class="sxs-lookup"><span data-stu-id="79795-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="79795-268">Seleccione **Web** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="79795-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="79795-269">Seleccione **Archivo HTML vacío** en el panel central.</span><span class="sxs-lookup"><span data-stu-id="79795-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="79795-270">Escriba *Index.cshtml* en el cuadro **Nombre**.</span><span class="sxs-lookup"><span data-stu-id="79795-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="79795-271">Seleccione **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="79795-271">Select **New**.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="79795-273">Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:</span><span class="sxs-lookup"><span data-stu-id="79795-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="79795-274">Navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="79795-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79795-275">El método `Index` en `HelloWorldController` no hizo mucho; ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="79795-276">Dado que no se especificó un nombre de archivo de plantilla de vista, MVC usa el archivo de vista predeterminado.</span><span class="sxs-lookup"><span data-stu-id="79795-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="79795-277">Este archivo tiene el mismo nombre que el método (`Index`), por lo que se usa en */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="79795-278">La imagen siguiente muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista)</span><span class="sxs-lookup"><span data-stu-id="79795-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="79795-279">codificada de forma rígida en la vista.</span><span class="sxs-lookup"><span data-stu-id="79795-279">hard-coded in the view.</span></span>

![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="79795-281">Cambio de vistas y páginas de diseño</span><span class="sxs-lookup"><span data-stu-id="79795-281">Change views and layout pages</span></span>

<span data-ttu-id="79795-282">Seleccione los vínculos de menú (**MvcMovie** [Película de MVC], **Home** [Inicio] y **Privacy** [Privacidad]).</span><span class="sxs-lookup"><span data-stu-id="79795-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="79795-283">Cada página muestra el mismo diseño de menú.</span><span class="sxs-lookup"><span data-stu-id="79795-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="79795-284">El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="79795-285">Abra el archivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="79795-286">Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio.</span><span class="sxs-lookup"><span data-stu-id="79795-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="79795-287">Busque la línea `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="79795-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="79795-288">`RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="79795-289">Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="79795-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="79795-290">Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="79795-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="79795-291">En los elementos de título y pie de página, cambie `MvcMovie` por `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="79795-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="79795-292">Cambie el delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` por `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="79795-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="79795-293">En el marcado siguiente se muestran los cambios:</span><span class="sxs-lookup"><span data-stu-id="79795-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="79795-294">En el marcado anterior, se omitió el `asp-area` [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) porque esta aplicación no utiliza [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="79795-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="79795-295">**Nota**: El controlador `Movies` no se ha implementado.</span><span class="sxs-lookup"><span data-stu-id="79795-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="79795-296">En este momento, el vínculo `Movie App` no es funcional.</span><span class="sxs-lookup"><span data-stu-id="79795-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="79795-297">Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad).</span><span class="sxs-lookup"><span data-stu-id="79795-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="79795-298">Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):</span><span class="sxs-lookup"><span data-stu-id="79795-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="79795-300">Pulse el vínculo **Home** (Inicio) y observe que el texto del título y el delimitador también muestran **Movie App** (Aplicación de película).</span><span class="sxs-lookup"><span data-stu-id="79795-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="79795-301">Hemos realizado el cambio una vez en la plantilla de diseño y hemos conseguido que todas las páginas del sitio reflejen el nuevo texto de vínculo y el nuevo título.</span><span class="sxs-lookup"><span data-stu-id="79795-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="79795-302">Examine el archivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79795-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="79795-303">El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista.</span><span class="sxs-lookup"><span data-stu-id="79795-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="79795-304">Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="79795-305">Cambie el título y el elemento `<h2>` del archivo de vista *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79795-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="79795-306">El título y el elemento `<h2>` son algo diferentes para que pueda ver qué parte del código cambia la presentación.</span><span class="sxs-lookup"><span data-stu-id="79795-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="79795-307">En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas).</span><span class="sxs-lookup"><span data-stu-id="79795-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="79795-308">La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:</span><span class="sxs-lookup"><span data-stu-id="79795-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="79795-309">Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="79795-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="79795-310">Tenga en cuenta que el título del explorador, el encabezado principal y los encabezados secundarios han cambiado.</span><span class="sxs-lookup"><span data-stu-id="79795-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="79795-311">(Si no ve los cambios en el explorador, es posible que esté viendo contenido almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="79795-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="79795-312">Presione Ctrl+F5 en el explorador para forzar que se cargue la respuesta del servidor). El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="79795-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="79795-313">Observe también cómo el contenido de la plantilla de vista *Index.cshtml* se fusionó con la plantilla de vista *Views/Shared/_Layout.cshtml* y se envió una única respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="79795-314">Con las plantillas de diseño es realmente fácil hacer cambios para que se apliquen en todas las páginas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="79795-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="79795-315">Para saber más, vea [Layout](xref:mvc/views/layout) (Diseño).</span><span class="sxs-lookup"><span data-stu-id="79795-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="79795-317">Nuestra pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista),</span><span class="sxs-lookup"><span data-stu-id="79795-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="79795-318">están codificados de forma rígida.</span><span class="sxs-lookup"><span data-stu-id="79795-318">message) is hard-coded, though.</span></span> <span data-ttu-id="79795-319">La aplicación de MVC tiene una "V" (vista) y ha obtenido una "C" (controlador), pero todavía no tiene una "M" (modelo).</span><span class="sxs-lookup"><span data-stu-id="79795-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="79795-320">Pasar datos del controlador a la vista</span><span class="sxs-lookup"><span data-stu-id="79795-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="79795-321">Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante.</span><span class="sxs-lookup"><span data-stu-id="79795-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="79795-322">Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="79795-323">El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="79795-324">Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="79795-325">Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta.</span><span class="sxs-lookup"><span data-stu-id="79795-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="79795-326">Procedimiento recomendado: las plantillas de vista **no** deben realizar lógica de negocios ni interactuar directamente con una base de datos.</span><span class="sxs-lookup"><span data-stu-id="79795-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="79795-327">En su lugar, una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador.</span><span class="sxs-lookup"><span data-stu-id="79795-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="79795-328">Mantener esta "separación de intereses" ayuda a mantener el código limpio, fácil de probar y de mantener.</span><span class="sxs-lookup"><span data-stu-id="79795-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="79795-329">Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="79795-330">En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="79795-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="79795-331">La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar las partes de datos adecuadas desde el controlador a la vista para que se genere la respuesta.</span><span class="sxs-lookup"><span data-stu-id="79795-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="79795-332">Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData` al que luego pueda obtener acceso la plantilla de vista.</span><span class="sxs-lookup"><span data-stu-id="79795-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="79795-333">En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79795-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="79795-334">El diccionario `ViewData` es un objeto dinámico, lo que significa que puede utilizarse cualquier tipo; el objeto `ViewData` no tiene ninguna propiedad definida hasta que coloca algo dentro de él.</span><span class="sxs-lookup"><span data-stu-id="79795-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="79795-335">El [sistema de enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre (`name` y `numTimes`) de la cadena de consulta en la barra de dirección a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="79795-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="79795-336">El archivo *HelloWorldController.cs* completo tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="79795-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="79795-337">El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.</span><span class="sxs-lookup"><span data-stu-id="79795-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="79795-338">Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79795-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="79795-339">Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="79795-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="79795-340">Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="79795-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="79795-341">Guarde los cambios y vaya a esta dirección URL:</span><span class="sxs-lookup"><span data-stu-id="79795-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="79795-342">Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="79795-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="79795-343">El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista.</span><span class="sxs-lookup"><span data-stu-id="79795-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="79795-344">Después, la vista representa los datos como HTML en el explorador.</span><span class="sxs-lookup"><span data-stu-id="79795-344">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="79795-346">En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="79795-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="79795-347">Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista.</span><span class="sxs-lookup"><span data-stu-id="79795-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="79795-348">El enfoque del modelo de vista que consiste en pasar datos suele ser más preferible que el enfoque de diccionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="79795-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="79795-349">Consulte [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (Cuándo usar ViewBag, ViewData o TempData) para más información.</span><span class="sxs-lookup"><span data-stu-id="79795-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="79795-350">En el tutorial siguiente crearemos una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="79795-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="79795-351">[Anterior](adding-controller.md)
> [Siguiente](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="79795-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
