---
title: Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 3 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 45ae6059e039ccfb4d835c615b660abc39251530
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021996"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a>Parte 3. Adición de una vista a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

En esta sección, se modificará la clase `HelloWorldController` para usar los archivos de vista de [Razor](xref:mvc/views/razor) con el objetivo de encapsular correctamente el proceso de generar respuestas HTML a un cliente.

Para crear un archivo de plantilla de vista se usa Razor. Las plantillas de vista basadas en Razor tienen una extensión de archivo *.cshtml*. Ofrecen una forma elegante de crear un resultado HTML con C#.

Actualmente, el método `Index` devuelve una cadena con un mensaje que está codificado de forma rígida en la clase de controlador. En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

El código anterior llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador. Este usa una plantilla de vista para generar una respuesta HTML. Los métodos de controlador (también conocidos como *métodos de acción*), como el método `Index` anterior, suelen devolver un valor <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, en lugar de un tipo como una cadena `string`.

## <a name="add-a-view"></a>Agregar una vista

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.

* Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.

* En el cuadro de diálogo **Agregar nuevo elemento - MvcMovie**

  * En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.

  * Seleccione **Vista de Razor** .

  * Conserve el valor del cuadro **Nombre**, *Index.cshtml*.

  * Seleccione **Agregar**.

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Agregue una vista `Index` para el `HelloWorldController`.

* Agregue una nueva carpeta denominada *Views/HelloWorld*.
* Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.
* Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo archivo**.
* En el cuadro de diálogo **Nuevo archivo**:

  * Seleccione **ASP .NET Core** en el panel izquierdo.
  * Seleccione **Página de la vista de MVC** en el panel central.
  * Escriba *Índice* en el cuadro **Nombre**.
  * Seleccione **Nuevo**.

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_mac.png)

---

Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Navegue a `https://localhost:{PORT}/HelloWorld`. El método `Index` en `HelloWorldController` no hizo mucho; ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador. Dado que no se especificó un nombre de archivo de plantilla de vista, MVC usa el archivo de vista predeterminado. Este archivo tiene el mismo nombre que el método (`Index`), por lo que se usa la plantilla de vista en */Views/HelloWorld/Index.cshtml*. La imagen siguiente muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista) codificada de forma rígida en la vista.

![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Cambio de vistas y páginas de diseño

Seleccione los vínculos de menú (**MvcMovie** [Película de MVC], **Home** [Inicio] y **Privacy** [Privacidad]). Cada página muestra el mismo diseño de menú. El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*. Abra el archivo *Views/Shared/_Layout.cshtml*.

Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio. Busque la línea `@RenderBody()`. `RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño. Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño

Reemplace el contenido del archivo *Views/Shared/_Layout.cshtml* por el marcado siguiente. Se resaltan los cambios:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

En el marcado anterior se realizan los cambios siguientes:

* 3 apariciones de `MvcMovie` a `Movie App`.
* El delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` a `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

En el marcado anterior, se omitieron el `asp-area=""` [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) y el valor de atributo porque esta aplicación no utiliza [Áreas](xref:mvc/controllers/areas).

**Nota**: El controlador `Movies` no se ha implementado. En este momento, el vínculo `Movie App` no es funcional.

Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad). Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Pulse el vínculo **Home** (Inicio) y observe que el texto del título y el delimitador también muestran **Movie App** (Aplicación de película). Hemos realizado el cambio una vez en la plantilla de diseño y hemos conseguido que todas las páginas del sitio reflejen el nuevo texto de vínculo y el nuevo título.

Examine el archivo *Views/_ViewStart.cshtml*:

```cshtml
@{
    Layout = "_Layout";
}
```

El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista. Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.

Cambie el título y el elemento `<h2>` del archivo de vista *Views/HelloWorld/Index.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

El título y el elemento `<h2>` son algo diferentes para que pueda ver qué parte del código cambia la presentación.

En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas). La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`. Tenga en cuenta que el título del explorador, el encabezado principal y los encabezados secundarios han cambiado. (Si no ve los cambios en el explorador, es posible que esté viendo contenido almacenado en caché. Presione Ctrl+F5 en el explorador para forzar que se cargue la respuesta del servidor). El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.

El contenido de la plantilla de vista *Index.cshtml* se combina con la plantilla de vista *Views/Shared/_Layout.cshtml*. Se envía una única respuesta HTML al explorador. Con las plantillas de diseño es fácil hacer cambios que se apliquen en todas las páginas de una aplicación. Para obtener más información, vea [Diseño](xref:mvc/views/layout).

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nuestra pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista), están codificados de forma rígida. La aplicación de MVC tiene una "V" (vista) y ha obtenido una "C" (controlador), pero todavía no tiene una "M" (modelo).

## <a name="passing-data-from-the-controller-to-the-view"></a>Pasar datos del controlador a la vista

Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante. Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador. El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador. Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.

Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta. Procedimiento recomendado: las plantillas de vista **no** deben realizar lógica de negocios ni interactuar directamente con una base de datos. En su lugar, una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador. Mantener esta "separación de intereses" ayuda a mantener el código limpio, fácil de probar y de mantener.

Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador. En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista. La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar las partes de datos adecuadas desde el controlador a la vista para que se genere la respuesta. Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData` al que luego pueda obtener acceso la plantilla de vista.

En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`. El diccionario `ViewData` es un objeto dinámico, lo que significa que puede utilizarse cualquier tipo; el objeto `ViewData` no tiene ninguna propiedad definida hasta que coloca algo dentro de él. El [sistema de enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre (`name` y `numTimes`) de la cadena de consulta en la barra de dirección a los parámetros del método. El archivo *HelloWorldController.cs* completo tiene este aspecto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.

Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.

Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`. Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Guarde los cambios y vaya a esta dirección URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos MVC](xref:mvc/models/model-binding). El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista. Después, la vista representa los datos como HTML en el explorador.

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista. Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista. El enfoque del modelo de vista que consiste en pasar datos suele ser más preferible que el enfoque de diccionario `ViewData`. Consulte [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (Cuándo usar ViewBag, ViewData o TempData) para más información.

En el tutorial siguiente crearemos una base de datos de películas.

> [!div class="step-by-step"]
> [Anterior](adding-controller.md)
> [Siguiente](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En esta sección, se modificará la clase `HelloWorldController` para usar los archivos de vista de [Razor](xref:mvc/views/razor) con el objetivo de encapsular correctamente el proceso de generar respuestas HTML a un cliente.

Para crear un archivo de plantilla de vista se usa Razor. Las plantillas de vista basadas en Razor tienen una extensión de archivo *.cshtml*. Ofrecen una forma elegante de crear un resultado HTML con C#.

Actualmente, el método `Index` devuelve una cadena con un mensaje que está codificado de forma rígida en la clase de controlador. En la clase `HelloWorldController`, reemplace el método `Index` por el siguiente código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

El código anterior llama al método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controlador. Este usa una plantilla de vista para generar una respuesta HTML. Los métodos de controlador (también conocidos como *métodos de acción*), como el método `Index` anterior, suelen devolver un valor <xref:Microsoft.AspNetCore.Mvc.IActionResult> o una clase derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>, en lugar de un tipo como una cadena `string`.

## <a name="add-a-view"></a>Agregar una vista

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.

* Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo elemento**.

* En el cuadro de diálogo **Agregar nuevo elemento - MvcMovie**

  * En el cuadro de búsqueda situado en la esquina superior derecha, escriba *Vista*.

  * Seleccione **Vista de Razor** .

  * Conserve el valor del cuadro **Nombre**, *Index.cshtml*.

  * Seleccione **Agregar**.

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Agregue una vista `Index` para el `HelloWorldController`.

* Agregue una nueva carpeta denominada *Views/HelloWorld*.
* Agregue un nuevo archivo a la carpeta *Views/HelloWorld* y asígnele el nombre *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón derecho en la carpeta *Vistas*, haga clic en **Agregar > Nueva carpeta** y asigne a la carpeta el nombre *HelloWorld*.
* Haga clic con el botón derecho en la carpeta *Views/HelloWorld* y, luego, haga clic en **Agregar > Nuevo archivo**.
* En el cuadro de diálogo **Nuevo archivo**:

  * Seleccione **Web** en el panel izquierdo.
  * Seleccione **Archivo HTML vacío** en el panel central.
  * Escriba *Index.cshtml* en el cuadro **Nombre**.
  * Seleccione **Nuevo**.

![Cuadro de diálogo Agregar nuevo elemento](adding-view/_static/add_view_mac.png)

---

Reemplace el contenido del archivo de vista de Razor *Views/HelloWorld/Index.cshtml* por el siguiente:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Navegue a `https://localhost:{PORT}/HelloWorld`. El método `Index` en `HelloWorldController` no hizo mucho; ejecutó la instrucción `return View();`, que especificaba que el método debe usar un archivo de plantilla de vista para representar una respuesta al explorador. Dado que no se especificó un nombre de archivo de plantilla de vista, MVC usa el archivo de vista predeterminado. Este archivo tiene el mismo nombre que el método (`Index`), por lo que se usa en */Views/HelloWorld/Index.cshtml*. La imagen siguiente muestra la cadena "Hello from our View Template!" (Hola desde nuestra plantilla de vista) codificada de forma rígida en la vista.

![Ventana del explorador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Cambio de vistas y páginas de diseño

Seleccione los vínculos de menú (**MvcMovie** [Película de MVC], **Home** [Inicio] y **Privacy** [Privacidad]). Cada página muestra el mismo diseño de menú. El diseño de menú se implementa en el archivo *Views/Shared/_Layout.cshtml*. Abra el archivo *Views/Shared/_Layout.cshtml*.

Las plantillas de [diseño](xref:mvc/views/layout) permiten especificar el diseño del contenedor HTML del sitio en un solo lugar y, después, aplicarlo en varias páginas del sitio. Busque la línea `@RenderBody()`. `RenderBody` es un marcador de posición donde se mostrarán todas las páginas específicas de vista que cree, *encapsuladas* en la página de diseño. Por ejemplo, si selecciona el vínculo **Privacy** (Privacidad), la vista **Views/Home/Privacy.cshtml** se representa dentro del método `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Cambio de los vínculos del título, el pie de página y el menú en el archivo de diseño

* En los elementos de título y pie de página, cambie `MvcMovie` por `Movie App`.
* Cambie el delimitador `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` por `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

En el marcado siguiente se muestran los cambios:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

En el marcado anterior, se omitió el `asp-area` [atributo del asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) porque esta aplicación no utiliza [Áreas](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Nota**: El controlador `Movies` no se ha implementado. En este momento, el vínculo `Movie App` no es funcional.

Guarde los cambios y seleccione el vínculo **Privacy** (Privacidad). Observe cómo el título de la pestaña del explorador muestra ahora **Privacy Policy - Movie Ap** (Directiva de privacidad - Aplicación de película) en lugar de **Privacy Policy - Mvc Movie** (Directiva de privacidad - Aplicación de MVC):

![Pestaña Privacy (Privacidad)](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Pulse el vínculo **Home** (Inicio) y observe que el texto del título y el delimitador también muestran **Movie App** (Aplicación de película). Hemos realizado el cambio una vez en la plantilla de diseño y hemos conseguido que todas las páginas del sitio reflejen el nuevo texto de vínculo y el nuevo título.

Examine el archivo *Views/_ViewStart.cshtml*:

```cshtml
@{
    Layout = "_Layout";
}
```

El archivo *Views/_ViewStart.cshtml* trae el archivo *Views/Shared/_Layout.cshtml* a cada vista. Se puede usar la propiedad `Layout` para establecer una vista de diseño diferente o establecerla en `null` para que no se use ningún archivo de diseño.

Cambie el título y el elemento `<h2>` del archivo de vista *Views/HelloWorld/Index.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

El título y el elemento `<h2>` son algo diferentes para que pueda ver qué parte del código cambia la presentación.

En el código anterior, `ViewData["Title"] = "Movie List";` establece la propiedad `Title` del diccionario `ViewData` en "Movie List" (Lista de películas). La propiedad `Title` se usa en el elemento HTML `<title>` en la página de diseño:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Guarde el cambio y navegue a `https://localhost:{PORT}/HelloWorld`. Tenga en cuenta que el título del explorador, el encabezado principal y los encabezados secundarios han cambiado. (Si no ve los cambios en el explorador, es posible que esté viendo contenido almacenado en caché. Presione Ctrl+F5 en el explorador para forzar que se cargue la respuesta del servidor). El título del explorador se crea con `ViewData["Title"]`, que se definió en la plantilla de vista *Index.cshtml* y el texto "- Movie App" (-Aplicación de película) que se agregó en el archivo de diseño.

Observe también cómo el contenido de la plantilla de vista *Index.cshtml* se fusionó con la plantilla de vista *Views/Shared/_Layout.cshtml* y se envió una única respuesta HTML al explorador. Con las plantillas de diseño es realmente fácil hacer cambios para que se apliquen en todas las páginas de la aplicación. Para saber más, vea [Layout](xref:mvc/views/layout) (Diseño).

![Vista de lista de películas](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nuestra pequeña cantidad de "datos", en este caso, el mensaje "Hello from our View Template!" (Hola desde nuestra plantilla de vista), están codificados de forma rígida. La aplicación de MVC tiene una "V" (vista) y ha obtenido una "C" (controlador), pero todavía no tiene una "M" (modelo).

## <a name="passing-data-from-the-controller-to-the-view"></a>Pasar datos del controlador a la vista

Las acciones del controlador se invocan en respuesta a una solicitud de dirección URL entrante. Una clase de controlador es donde se escribe el código que controla las solicitudes entrantes del explorador. El controlador recupera datos de un origen de datos y decide qué tipo de respuesta devolverá al explorador. Las plantillas de vista se pueden usar desde un controlador para generar y dar formato a una respuesta HTML al explorador.

Los controladores se encargan de proporcionar los datos necesarios para que una plantilla de vista represente una respuesta. Procedimiento recomendado: las plantillas de vista **no** deben realizar lógica de negocios ni interactuar directamente con una base de datos. En su lugar, una plantilla de vista debe funcionar solo con los datos que le proporciona el controlador. Mantener esta "separación de intereses" ayuda a mantener el código limpio, fácil de probar y de mantener.

Actualmente, el método `Welcome` de la clase `HelloWorldController` toma un parámetro `name` y `ID`, y luego obtiene los valores directamente en el explorador. En lugar de que el controlador represente esta respuesta como una cadena, cambie el controlador para que use una plantilla de vista. La plantilla de vista genera una respuesta dinámica, lo que significa que se deben pasar las partes de datos adecuadas desde el controlador a la vista para que se genere la respuesta. Para hacerlo, indique al controlador que coloque los datos dinámicos (parámetros) que necesita la plantilla de vista en un diccionario `ViewData` al que luego pueda obtener acceso la plantilla de vista.

En *HelloWorldController.cs*, cambie el método `Welcome` para agregar un valor `Message` y `NumTimes` al diccionario `ViewData`. El diccionario `ViewData` es un objeto dinámico, lo que significa que puede utilizarse cualquier tipo; el objeto `ViewData` no tiene ninguna propiedad definida hasta que coloca algo dentro de él. El [sistema de enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre (`name` y `numTimes`) de la cadena de consulta en la barra de dirección a los parámetros del método. El archivo *HelloWorldController.cs* completo tiene este aspecto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

El objeto de diccionario `ViewData` contiene datos que se pasarán a la vista.

Cree una plantilla de vista principal denominada *Views/HelloWorld/Welcome.cshtml*.

Se creará un bucle en la vista *Welcome.cshtml* que muestra "Hello" (Hola) `NumTimes`. Reemplace el contenido de *Views/HelloWorld/Welcome.cshtml* con lo siguiente:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Guarde los cambios y vaya a esta dirección URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Los datos se toman de la dirección URL y se pasan al controlador mediante el [enlazador de modelos MVC](xref:mvc/models/model-binding). El controlador empaqueta los datos en un diccionario `ViewData` y pasa ese objeto a la vista. Después, la vista representa los datos como HTML en el explorador.

![Vista Privacy (Privacidad) que muestra una etiqueta Welcome (Bienvenida) y la frase "Hello Rick" (Hola Rick) cuatro veces](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

En el ejemplo anterior, se usó el diccionario `ViewData` para pasar datos del controlador a una vista. Más adelante en el tutorial usaremos un modelo de vista para pasar datos de un controlador a una vista. El enfoque del modelo de vista que consiste en pasar datos suele ser más preferible que el enfoque de diccionario `ViewData`. Consulte [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (Cuándo usar ViewBag, ViewData o TempData) para más información.

En el tutorial siguiente crearemos una base de datos de películas.

> [!div class="step-by-step"]
> [Anterior](adding-controller.md)
> [Siguiente](adding-model.md)

::: moniker-end
