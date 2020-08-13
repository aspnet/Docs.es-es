---
title: Parte 6. Vistas y métodos de controlador en ASP.NET Core
author: rick-anderson
description: Parte 6. Adición de un modelo a una aplicación de ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b5b54adb1d86e4ab982647dc8207839cc9afdac9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021073"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="a83ee-103">Parte 6. Vistas y métodos de controlador en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a83ee-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="a83ee-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a83ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a83ee-105">La aplicación de películas tiene buena pinta, pero la presentación no es la ideal. Por ejemplo, **FechaDeLanzamiento** debería escribirse en tres palabras.</span><span class="sxs-lookup"><span data-stu-id="a83ee-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Vista de índice: ReleaseDate es una sola palabra (sin espacios) y en todas las fechas de lanzamiento de películas se muestra una hora de 12 a. m.](working-with-sql/_static/m55.png)

<span data-ttu-id="a83ee-107">Abra el archivo *Models/Movie.cs* y agregue las líneas resaltadas que se muestran a continuación:</span><span class="sxs-lookup"><span data-stu-id="a83ee-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="a83ee-108">En el próximo tutorial hablaremos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="a83ee-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="a83ee-109">El atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica qué se muestra como nombre de un campo (en este caso, "Release Date" en lugar de "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="a83ee-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="a83ee-110">El atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de los datos (Date), así que la información de hora almacenada en el campo no se muestra.</span><span class="sxs-lookup"><span data-stu-id="a83ee-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="a83ee-111">La anotación de datos `[Column(TypeName = "decimal(18, 2)")]` es necesaria para que Entity Framework Core asigne correctamente `Price` a la moneda en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a83ee-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="a83ee-112">Para más información, vea [Tipos de datos](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="a83ee-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="a83ee-113">Vaya al controlador `Movies` y mantenga el puntero del mouse sobre un vínculo **Edit** (Editar) para ver la dirección URL de destino.</span><span class="sxs-lookup"><span data-stu-id="a83ee-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Ventana del explorador con el mouse sobre el vínculo Edit (Editar) donde se muestra una dirección URL de vínculo https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="a83ee-115">Los vínculos **Edit** (Editar), **Details** (Detalles) y **Delete** (Eliminar) se generan mediante el asistente de etiquetas de delimitador de MVC Core en el archivo *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a83ee-115">The **Edit**, **Details**, and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="a83ee-116">Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.</span><span class="sxs-lookup"><span data-stu-id="a83ee-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a83ee-117">En el código anterior, `AnchorTagHelper` genera dinámicamente el valor del atributo HTML `href` a partir del identificador de ruta y el método de acción del controlador. Use **Ver código fuente** en su explorador preferido o use las herramientas de desarrollo para examinar el marcado generado.</span><span class="sxs-lookup"><span data-stu-id="a83ee-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="a83ee-118">A continuación se muestra una parte del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="a83ee-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="a83ee-119">Recupere el formato para el [enrutamiento](xref:mvc/controllers/routing) establecido en el archivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83ee-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="a83ee-120">ASP.NET Core traduce `https://localhost:5001/Movies/Edit/4` en una solicitud al método de acción `Edit` del controlador `Movies` con el parámetro `Id` de 4.</span><span class="sxs-lookup"><span data-stu-id="a83ee-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="a83ee-121">(Los métodos de controlador también se denominan métodos de acción).</span><span class="sxs-lookup"><span data-stu-id="a83ee-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="a83ee-122">Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) son una de las nuevas características más populares de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a83ee-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="a83ee-123">Para obtener más información, consulte [Recursos adicionales](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="a83ee-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="a83ee-124">Abra el controlador `Movies` y examine los dos métodos de acción `Edit`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="a83ee-125">En el código siguiente se muestra el método `HTTP GET Edit`, que captura la película y rellena el formulario de edición generado por el archivo de Razor *Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a83ee-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* Razor file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="a83ee-126">En el código siguiente se muestra el método `HTTP POST Edit`, que procesa los valores de película publicados:</span><span class="sxs-lookup"><span data-stu-id="a83ee-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="a83ee-127">En el código siguiente se muestra el método `HTTP POST Edit`, que procesa los valores de película publicados:</span><span class="sxs-lookup"><span data-stu-id="a83ee-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="a83ee-128">El atributo `[Bind]` es una manera de proteger contra el [exceso de publicación](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="a83ee-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="a83ee-129">Solo debe incluir propiedades en el atributo `[Bind]` que quiera cambiar.</span><span class="sxs-lookup"><span data-stu-id="a83ee-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="a83ee-130">Para más información, consulte [Protección del controlador frente al exceso de publicación](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="a83ee-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="a83ee-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) ofrece un enfoque alternativo para evitar el exceso de publicaciones.</span><span class="sxs-lookup"><span data-stu-id="a83ee-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="a83ee-132">Observe que el segundo método de acción `Edit` va precedido del atributo `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="a83ee-133">El atributo `HttpPost` especifica que este método `Edit` se puede invocar *solamente* para solicitudes `POST`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="a83ee-134">Podría aplicar el atributo `[HttpGet]` al primer método de edición, pero no es necesario hacerlo porque `[HttpGet]` es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a83ee-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="a83ee-135">El atributo `ValidateAntiForgeryToken` se usa para [impedir la falsificación de una solicitud](xref:security/anti-request-forgery) y se empareja con un token antifalsificación generado en el archivo de vista de edición (*Views/Movies/Edit.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="a83ee-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file (*Views/Movies/Edit.cshtml*).</span></span> <span data-ttu-id="a83ee-136">El archivo de vista de edición genera el token antifalsificación con el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="a83ee-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="a83ee-137">El [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms) genera un token antifalsificación oculto que debe coincidir con el token antifalsificación generado por `[ValidateAntiForgeryToken]` en el método `Edit` del controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="a83ee-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="a83ee-138">Para más información, vea [Prevención de ataques de falsificación de solicitudes](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="a83ee-138">For more information, see [Anti-Request Forgery](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="a83ee-139">El método `HttpGet Edit` toma el parámetro `ID` de la película, busca la película con el método `FindAsync` de Entity Framework y devuelve la película seleccionada a la vista de edición.</span><span class="sxs-lookup"><span data-stu-id="a83ee-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="a83ee-140">Si no se encuentra una película, se devuelve `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="a83ee-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="a83ee-141">Cuando el sistema de scaffolding creó la vista de edición, examinó la clase `Movie` y creó código para representar los elementos `<label>` y `<input>` para cada propiedad de la clase.</span><span class="sxs-lookup"><span data-stu-id="a83ee-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="a83ee-142">En el ejemplo siguiente se muestra la vista de edición que generó el sistema de scaffolding de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a83ee-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="a83ee-143">Observe cómo la plantilla de vista tiene una instrucción `@model MvcMovie.Models.Movie` en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="a83ee-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="a83ee-144">`@model MvcMovie.Models.Movie` especifica que la vista espera que el modelo de la plantilla de vista sea del tipo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="a83ee-145">El código con scaffolding usa varios métodos del asistente de etiquetas para simplificar el marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="a83ee-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="a83ee-146">El [asistente de etiquetas](xref:mvc/views/working-with-forms) muestra el nombre del campo: "Title" (Título), "ReleaseDate" (Fecha de lanzamiento), "Genre" (Género) o "Price" (Precio).</span><span class="sxs-lookup"><span data-stu-id="a83ee-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="a83ee-147">El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) representa un elemento HTML `<input>`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="a83ee-148">El [asistente de etiquetas de validación](xref:mvc/views/working-with-forms) muestra cualquier mensaje de validación asociado a esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="a83ee-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="a83ee-149">Ejecute la aplicación y navegue a la URL `/Movies`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="a83ee-150">Haga clic en un vínculo **Edit** (Editar).</span><span class="sxs-lookup"><span data-stu-id="a83ee-150">Click an **Edit** link.</span></span> <span data-ttu-id="a83ee-151">En el explorador, vea el código fuente de la página.</span><span class="sxs-lookup"><span data-stu-id="a83ee-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="a83ee-152">El código HTML generado para el elemento `<form>` se muestra abajo.</span><span class="sxs-lookup"><span data-stu-id="a83ee-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="a83ee-153">Los elementos `<input>` se muestran en un elemento `HTML <form>` cuyo atributo `action` se establece para publicar en la dirección URL `/Movies/Edit/id`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="a83ee-154">Los datos del formulario se publicarán en el servidor cuando se haga clic en el botón `Save`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="a83ee-155">La última línea antes del cierre del elemento `</form>` muestra el token [XSRF](xref:security/anti-request-forgery) oculto generado por el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="a83ee-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="a83ee-156">Procesamiento de la solicitud POST</span><span class="sxs-lookup"><span data-stu-id="a83ee-156">Processing the POST Request</span></span>

<span data-ttu-id="a83ee-157">En la siguiente lista se muestra la versión `[HttpPost]` del método de acción `Edit`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="a83ee-158">El atributo `[ValidateAntiForgeryToken]` valida el token [XSRF](xref:security/anti-request-forgery) oculto generado por el generador de tokens antifalsificación en el [asistente de etiquetas de formulario](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="a83ee-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="a83ee-159">El sistema de [enlace de modelos](xref:mvc/models/model-binding) toma los valores de formulario publicados y crea un objeto `Movie` que se pasa como el parámetro `movie`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="a83ee-160">El método `ModelState.IsValid` comprueba que los datos presentados en el formulario pueden usarse para modificar (editar o actualizar) un objeto `Movie`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-160">The `ModelState.IsValid` method verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="a83ee-161">Si los datos son válidos, se guardan.</span><span class="sxs-lookup"><span data-stu-id="a83ee-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="a83ee-162">Los datos de película actualizados (o modificados) se guardan en la base de datos mediante una llamada al método `SaveChangesAsync` del contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="a83ee-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="a83ee-163">Después de guardar los datos, el código redirige al usuario al método de acción `Index` de la clase `MoviesController`, que muestra la colección de películas, incluidos los cambios que se acaban de hacer.</span><span class="sxs-lookup"><span data-stu-id="a83ee-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="a83ee-164">Antes de que el formulario se publique en el servidor, la validación del lado cliente comprueba las reglas de validación en los campos.</span><span class="sxs-lookup"><span data-stu-id="a83ee-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="a83ee-165">Si hay errores de validación, se muestra un mensaje de error y no se publica el formulario.</span><span class="sxs-lookup"><span data-stu-id="a83ee-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="a83ee-166">Si JavaScript está deshabilitado, no dispondrá de la validación del lado cliente, sino que el servidor detectará los valores publicados que no son válidos y los valores de formulario se volverán a mostrar con mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="a83ee-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="a83ee-167">Más adelante en el tutorial se examina la [validación de modelos](xref:mvc/models/validation) con más detalle.</span><span class="sxs-lookup"><span data-stu-id="a83ee-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="a83ee-168">El [asistente de etiquetas de validación](xref:mvc/views/working-with-forms) en la plantilla de vista *Views/Movies/Edit.cshtml* se encarga de mostrar los mensajes de error correspondientes.</span><span class="sxs-lookup"><span data-stu-id="a83ee-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Vista de edición: excepción de un valor de precio incorrecto de los estados abc que indica que el campo de precio debe ser un número.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="a83ee-171">Todos los métodos `HttpGet` del controlador de películas siguen un patrón similar.</span><span class="sxs-lookup"><span data-stu-id="a83ee-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="a83ee-172">Obtienen un objeto de película (o una lista de objetos, en el caso de `Index`) y pasan el objeto (modelo) a la vista.</span><span class="sxs-lookup"><span data-stu-id="a83ee-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="a83ee-173">El método `Create` pasa un objeto de película vacío a la vista `Create`.</span><span class="sxs-lookup"><span data-stu-id="a83ee-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="a83ee-174">Todos los métodos que crean, editan, eliminan o modifican los datos lo hacen en la sobrecarga `[HttpPost]` del método.</span><span class="sxs-lookup"><span data-stu-id="a83ee-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="a83ee-175">La modificación de datos en un método `HTTP GET` supone un riesgo de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a83ee-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="a83ee-176">La modificación de datos en un método `HTTP GET` también infringe procedimientos recomendados de HTTP y el patrón de arquitectura [REST](http://rest.elkstein.org/), que especifica que las solicitudes GET no deben cambiar el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a83ee-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="a83ee-177">En otras palabras, realizar una operación GET debería ser una operación segura sin efectos secundarios, que no modifica los datos persistentes.</span><span class="sxs-lookup"><span data-stu-id="a83ee-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a83ee-178">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a83ee-178">Additional resources</span></span>

* [<span data-ttu-id="a83ee-179">Globalización y localización</span><span class="sxs-lookup"><span data-stu-id="a83ee-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="a83ee-180">Introducción a los asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="a83ee-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a83ee-181">Creación de asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="a83ee-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="a83ee-182">Prevención de ataques de falsificación de solicitudes</span><span class="sxs-lookup"><span data-stu-id="a83ee-182">Anti-Request Forgery</span></span>](xref:security/anti-request-forgery)
* <span data-ttu-id="a83ee-183">Protección del controlador frente al [exceso de publicación](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="a83ee-183">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="a83ee-184">ViewModels</span><span class="sxs-lookup"><span data-stu-id="a83ee-184">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="a83ee-185">Asistente de etiquetas de formulario</span><span class="sxs-lookup"><span data-stu-id="a83ee-185">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="a83ee-186">Asistente de etiquetas de entrada</span><span class="sxs-lookup"><span data-stu-id="a83ee-186">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="a83ee-187">Asistente de etiquetas de elementos de etiqueta</span><span class="sxs-lookup"><span data-stu-id="a83ee-187">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="a83ee-188">Asistente de etiquetas de selección</span><span class="sxs-lookup"><span data-stu-id="a83ee-188">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="a83ee-189">Asistente de etiquetas de validación</span><span class="sxs-lookup"><span data-stu-id="a83ee-189">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="a83ee-190">[Anterior](working-with-sql.md)
> [Siguiente](search.md)</span><span class="sxs-lookup"><span data-stu-id="a83ee-190">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
