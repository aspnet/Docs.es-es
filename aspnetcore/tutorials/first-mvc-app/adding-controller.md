---
title: Parte 2. Adición de un controlador a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 2 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975266"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4ef47-103">Parte 2. Adición de un controlador a una aplicación de ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4ef47-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4ef47-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ef47-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ef47-105">El patrón de arquitectura de Modelo-Vista-Controlador (MVC) separa una aplicación en tres componentes principales: **M** odelo, **v** ista y **c** ontrolador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="4ef47-106">El patrón de MVC ayuda a crear aplicaciones que son más fáciles de actualizar y probar que las tradicionales aplicaciones monolíticas.</span><span class="sxs-lookup"><span data-stu-id="4ef47-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="4ef47-107">Las aplicaciones basadas en MVC contienen:</span><span class="sxs-lookup"><span data-stu-id="4ef47-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="4ef47-108">**M** odelos: clases que representan los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ef47-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="4ef47-109">Las clases de modelo usan lógica de validación para aplicar las reglas de negocio para esos datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="4ef47-110">Normalmente, los objetos de modelo recuperan y almacenan el estado del modelo en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="4ef47-111">En este tutorial, un modelo `Movie` recupera datos de películas de una base de datos, los proporciona a la vista o los actualiza.</span><span class="sxs-lookup"><span data-stu-id="4ef47-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="4ef47-112">Los datos actualizados se escriben en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="4ef47-113">**V** istas: Las vistas son los componentes que muestran la interfaz de usuario (IU) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ef47-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="4ef47-114">Por lo general, esta interfaz de usuario muestra los datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="4ef47-115">Los **c** ontroladores son clases que:</span><span class="sxs-lookup"><span data-stu-id="4ef47-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="4ef47-116">Controlan las solicitudes del explorador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-116">Handle browser requests.</span></span>
  * <span data-ttu-id="4ef47-117">Recuperan datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-117">Retrieve model data.</span></span>
  * <span data-ttu-id="4ef47-118">Llaman a plantillas de vista que devuelven una respuesta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-118">Call view templates that return a response.</span></span>

<span data-ttu-id="4ef47-119">En una aplicación MVC, la vista solo muestra información.</span><span class="sxs-lookup"><span data-stu-id="4ef47-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="4ef47-120">El controlador controla la entrada y la interacción del usuario y responde a estas.</span><span class="sxs-lookup"><span data-stu-id="4ef47-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="4ef47-121">Por ejemplo, el controlador controla los segmentos de URL y los valores de cadena de consulta y pasa estos valores al modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="4ef47-122">El modelo puede usar estos valores para consultar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-122">The model might use these values to query the database.</span></span> <span data-ttu-id="4ef47-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4ef47-123">For example:</span></span>

* <span data-ttu-id="4ef47-124">`Https://localhost:5001/Home/Privacy`: especifica el controlador `Home` y la acción `Privacy`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="4ef47-125">`Https://localhost:5001/Movies/Edit/5`: es una solicitud para editar la película con ID=5 mediante el controlador `Movies` y la acción `Edit`, que se detallan más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="4ef47-126">Los datos de ruta se explican más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4ef47-127">El patrón de arquitectura de MVC separa una aplicación en tres grupos de componentes principales: modelos, vistas y controladores.</span><span class="sxs-lookup"><span data-stu-id="4ef47-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="4ef47-128">Este patrón permite lograr la separación de intereses, puesto que la lógica de la interfaz de usuario pertenece a la vista.</span><span class="sxs-lookup"><span data-stu-id="4ef47-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="4ef47-129">La lógica de entrada pertenece al controlador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="4ef47-130">La lógica de negocios pertenece al modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-130">Business logic belongs in the model.</span></span> <span data-ttu-id="4ef47-131">Esta separación ayuda a administrar la complejidad al compilar una aplicación, ya que permite trabajar en un aspecto de la implementación de cada vez, sin influir en el código de otro.</span><span class="sxs-lookup"><span data-stu-id="4ef47-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="4ef47-132">Por ejemplo, puede trabajar en el código de vista sin depender del código de lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="4ef47-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="4ef47-133">Estos conceptos se presentan y se muestran en esta serie de tutoriales mientras se compila una aplicación de películas.</span><span class="sxs-lookup"><span data-stu-id="4ef47-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="4ef47-134">El proyecto de MVC contiene carpetas para *controladores* y *vistas*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4ef47-135">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="4ef47-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ef47-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef47-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4ef47-137">En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Controlador**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![En el Explorador de soluciones, haga clic con el botón derecho en Controladores > Agregar > Controlador.](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="4ef47-139">En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador de MVC: en blanco**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="4ef47-141">En el **cuadro de diálogo Agregar nuevo elemento: MvcMovie**, escriba **HelloWorldController.cs** y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ef47-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef47-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4ef47-143">Seleccione el icono **EXPLORADOR**, presione Ctrl y haga clic con el botón derecho en **Controladores > Nuevo archivo** y asigne al nuevo archivo el nombre *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Menú contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ef47-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ef47-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4ef47-146">En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menú contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="4ef47-148">Seleccione **ASP.NET Core** y **Clase de controlador**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="4ef47-149">Asigne al controlador el nombre **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-149">Name the controller **HelloWorldController**.</span></span>

![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="4ef47-151">Reemplace el contenido de *Controllers/HelloWorldController.cs* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4ef47-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="4ef47-152">Cada método `public` en un controlador puede ser invocado como un punto de conexión HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef47-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="4ef47-153">En el ejemplo anterior, ambos métodos devuelven una cadena.</span><span class="sxs-lookup"><span data-stu-id="4ef47-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="4ef47-154">Observe los comentarios delante de cada método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="4ef47-155">Un punto de conexión HTTP:</span><span class="sxs-lookup"><span data-stu-id="4ef47-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="4ef47-156">Es una dirección URL que se puede establecer como destino en la aplicación web, por ejemplo, `https://localhost:5001/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="4ef47-157">Combina:</span><span class="sxs-lookup"><span data-stu-id="4ef47-157">Combines:</span></span>
  * <span data-ttu-id="4ef47-158">El protocolo usado: `HTTPS`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="4ef47-159">La ubicación de red del servidor web, incluido el puerto TCP: `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="4ef47-160">El URI de destino: `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="4ef47-161">El primer comentario indica que se trata de un método [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) que se invoca anexando `/HelloWorld/` a la dirección URL base.</span><span class="sxs-lookup"><span data-stu-id="4ef47-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="4ef47-162">El segundo comentario especifica un método [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) que se invoca anexando `/HelloWorld/Welcome/` a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="4ef47-163">Más adelante en el tutorial se usa el motor de scaffolding para generar métodos `HTTP POST` que actualizan los datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="4ef47-164">Ejecute la aplicación sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-164">Run the app without the debugger.</span></span>

<span data-ttu-id="4ef47-165">Anexe "HelloWorld" a la ruta de acceso en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ef47-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="4ef47-166">El método `Index` devuelve una cadena.</span><span class="sxs-lookup"><span data-stu-id="4ef47-166">The `Index` method returns a string.</span></span>

![Ventana del explorador en la que se muestra una respuesta de la aplicación a "This is my default action" (Esta es mi acción predeterminada)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="4ef47-168">MVC invoca las clases del controlador y los métodos de acción que contienen, en función de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="4ef47-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="4ef47-169">La [lógica de enrutamiento de URL](xref:mvc/controllers/routing) predeterminada que usa MVC emplea un formato similar al siguiente para determinar qué código se debe invocar:</span><span class="sxs-lookup"><span data-stu-id="4ef47-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="4ef47-170">El formato de enrutamiento se establece en el método `Configure` del archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="4ef47-171">Cuando se navega a la aplicación y no se suministra ningún segmento de dirección URL, de manera predeterminada se usan el controlador "Home" y el método "Index" especificados en la línea de plantilla resaltada arriba.</span><span class="sxs-lookup"><span data-stu-id="4ef47-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="4ef47-172">En los segmentos de dirección URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="4ef47-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="4ef47-173">El primer segmento de dirección URL determina la clase de controlador que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="4ef47-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="4ef47-174">Por tanto, `localhost:5001/HelloWorld` se asigna a la clase **HelloWorld** Controller.</span><span class="sxs-lookup"><span data-stu-id="4ef47-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="4ef47-175">La segunda parte del segmento de dirección URL determina el método de acción en la clase.</span><span class="sxs-lookup"><span data-stu-id="4ef47-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="4ef47-176">Así pues, `localhost:5001/HelloWorld/Index` provoca que se ejecute el método `Index` de la clase `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="4ef47-177">Tenga en cuenta que solo es necesario navegar a `localhost:5001/HelloWorld` para que se llame al método `Index` de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4ef47-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="4ef47-178">`Index` es el método predeterminado al que se llamará en un controlador si no se especifica explícitamente un nombre de método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="4ef47-179">La tercera parte del segmento de dirección URL (`id`) es para los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="4ef47-180">Los datos de ruta se explican más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4ef47-181">Vaya a `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="4ef47-182">Reemplace `{PORT}` por el número de puerto.</span><span class="sxs-lookup"><span data-stu-id="4ef47-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="4ef47-183">El método `Welcome` se ejecuta y devuelve la cadena `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="4ef47-184">Para esta dirección URL, el controlador es `HelloWorld` y `Welcome` es el método de acción.</span><span class="sxs-lookup"><span data-stu-id="4ef47-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="4ef47-185">Todavía no ha usado el elemento `[Parameters]` de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Ventana del explorador que muestra la respuesta de la aplicación "This is the Welcome action method" (Este es el método de acción predeterminado)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="4ef47-187">Modifique el código para pasar cierta información del parámetro desde la dirección URL al controlador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="4ef47-188">Por ejemplo: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="4ef47-189">Cambie el método `Welcome` para que incluya dos parámetros, como se muestra en el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="4ef47-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="4ef47-190">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4ef47-190">The preceding code:</span></span>

* <span data-ttu-id="4ef47-191">Usa la característica de parámetro opcional de C# para indicar que el parámetro `numTimes` tiene el valor predeterminado 1 si no se pasa ningún valor para ese parámetro.</span><span class="sxs-lookup"><span data-stu-id="4ef47-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="4ef47-192">Usa `HtmlEncoder.Default.Encode` para proteger la aplicación de entradas malintencionadas, por ejemplo, mediante JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4ef47-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="4ef47-193">Usa [cadenas interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) en `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="4ef47-194">Ejecute la aplicación y vaya a `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="4ef47-195">Reemplace `{PORT}` por el número de puerto.</span><span class="sxs-lookup"><span data-stu-id="4ef47-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="4ef47-196">Pruebe distintos valores para `name` y `numtimes` en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="4ef47-197">El sistema de [enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre de la cadena de consulta a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="4ef47-198">Vea [Model Binding](xref:mvc/models/model-binding) (Enlace de modelos) para más información.</span><span class="sxs-lookup"><span data-stu-id="4ef47-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Ventana del explorador que muestra una respuesta de la aplicación de Hello Rick, NumTimes es\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="4ef47-200">En la imagen anterior:</span><span class="sxs-lookup"><span data-stu-id="4ef47-200">In the previous image:</span></span>

* <span data-ttu-id="4ef47-201">No se usa el segmento de dirección URL `Parameters`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="4ef47-202">Se pasan los parámetros `name` y `numTimes` en la [cadena de consulta](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="4ef47-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="4ef47-203">El `?` (signo de interrogación) en la dirección URL anterior es un separador y le sigue la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="4ef47-204">El carácter `&` separa los pares campo-valor.</span><span class="sxs-lookup"><span data-stu-id="4ef47-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="4ef47-205">Reemplace el método `Welcome` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4ef47-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="4ef47-206">Ejecute la aplicación y escriba la siguiente dirección URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="4ef47-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="4ef47-207">En la dirección URL anterior:</span><span class="sxs-lookup"><span data-stu-id="4ef47-207">In the preceding URL:</span></span>

* <span data-ttu-id="4ef47-208">El tercer segmento de dirección URL coincide con el parámetro de ruta `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="4ef47-209">El método `Welcome` contiene un parámetro `id` que coincide con la plantilla de dirección URL en el método `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="4ef47-210">El elemento `?` final (en `id?`) indica que el parámetro `id` es opcional.</span><span class="sxs-lookup"><span data-stu-id="4ef47-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="4ef47-211">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4ef47-211">In the preceding example:</span></span>

* <span data-ttu-id="4ef47-212">El tercer segmento de dirección URL coincide con el parámetro de ruta `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="4ef47-213">El método `Welcome` contiene un parámetro `id` que coincide con la plantilla de dirección URL en el método `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="4ef47-214">El elemento `?` final (en `id?`) indica que el parámetro `id` es opcional.</span><span class="sxs-lookup"><span data-stu-id="4ef47-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4ef47-215">[Anterior: Introducción](start-mvc.md)
> [Siguiente: Adición de una vista](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="4ef47-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ef47-216">El patrón de arquitectura de Modelo-Vista-Controlador (MVC) separa una aplicación en tres componentes principales: **M** odelo, **v** ista y **c** ontrolador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="4ef47-217">El patrón de MVC ayuda a crear aplicaciones que son más fáciles de actualizar y probar que las tradicionales aplicaciones monolíticas.</span><span class="sxs-lookup"><span data-stu-id="4ef47-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="4ef47-218">Las aplicaciones basadas en MVC contienen:</span><span class="sxs-lookup"><span data-stu-id="4ef47-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="4ef47-219">**M** odelos: clases que representan los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ef47-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="4ef47-220">Las clases de modelo usan lógica de validación para aplicar las reglas de negocio para esos datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="4ef47-221">Normalmente, los objetos de modelo recuperan y almacenan el estado del modelo en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="4ef47-222">En este tutorial, un modelo `Movie` recupera datos de películas de una base de datos, los proporciona a la vista o los actualiza.</span><span class="sxs-lookup"><span data-stu-id="4ef47-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="4ef47-223">Los datos actualizados se escriben en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="4ef47-224">**V** istas: Las vistas son los componentes que muestran la interfaz de usuario (IU) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ef47-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="4ef47-225">Por lo general, esta interfaz de usuario muestra los datos del modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="4ef47-226">**C** ontroladores: clases que controlan las solicitudes del explorador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="4ef47-227">Recuperan los datos del modelo y llaman a plantillas de vistas que devuelven una respuesta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="4ef47-228">En una aplicación MVC, la vista solo muestra información; el controlador controla la interacción de los usuarios y los datos que introducen, y responde a ellos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="4ef47-229">Por ejemplo, el controlador controla los datos de enrutamiento y los valores de cadena de consulta y pasa estos valores al modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="4ef47-230">El modelo puede usar estos valores para consultar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-230">The model might use these values to query the database.</span></span> <span data-ttu-id="4ef47-231">Por ejemplo, `https://localhost:5001/Home/About` tiene datos de enrutamiento de `Home` (el controlador) y `About` (el método de acción para llamar al controlador de inicio).</span><span class="sxs-lookup"><span data-stu-id="4ef47-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="4ef47-232">`https://localhost:5001/Movies/Edit/5` es una solicitud para editar la película con ID=5 mediante el controlador de películas.</span><span class="sxs-lookup"><span data-stu-id="4ef47-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="4ef47-233">Los datos de ruta se explican más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4ef47-234">El patrón de MVC ayuda a crear aplicaciones que separan los diferentes aspectos de la aplicación (lógica de entrada, lógica comercial y lógica de la interfaz de usuario), a la vez que proporciona un acoplamiento vago entre estos elementos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="4ef47-235">El patrón especifica dónde debe ubicarse cada tipo de lógica en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ef47-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="4ef47-236">La lógica de la interfaz de usuario pertenece a la vista.</span><span class="sxs-lookup"><span data-stu-id="4ef47-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="4ef47-237">La lógica de entrada pertenece al controlador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="4ef47-238">La lógica de negocios pertenece al modelo.</span><span class="sxs-lookup"><span data-stu-id="4ef47-238">Business logic belongs in the model.</span></span> <span data-ttu-id="4ef47-239">Esta separación ayuda a administrar la complejidad al compilar una aplicación, ya que permite trabajar en uno de los aspectos de la implementación a la vez sin influir en el código de otro.</span><span class="sxs-lookup"><span data-stu-id="4ef47-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="4ef47-240">Por ejemplo, puede trabajar en el código de vista sin depender del código de lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="4ef47-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="4ef47-241">En esta serie de tutoriales se tratarán estos conceptos y se mostrará cómo usarlos para crear una aplicación de película.</span><span class="sxs-lookup"><span data-stu-id="4ef47-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="4ef47-242">El proyecto de MVC contiene carpetas para *controladores* y *vistas*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4ef47-243">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="4ef47-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ef47-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef47-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef47-245">En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Controlador**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Menú contextual](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="4ef47-247">En el cuadro de diálogo **Agregar Scaffold**, seleccione **MVC Controller - Empty** (Controlador MVC: en blanco)</span><span class="sxs-lookup"><span data-stu-id="4ef47-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="4ef47-249">En el cuadro de diálogo **Add Empty MVC Controller** (Agregar controlador MVC en blanco), escriba **HelloWorldController** y seleccione **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ef47-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef47-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4ef47-251">Seleccione el icono **EXPLORADOR**, presione Ctrl y haga clic con el botón derecho en **Controladores > Nuevo archivo** y asigne al nuevo archivo el nombre *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Menú contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ef47-253">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ef47-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4ef47-254">En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Nuevo archivo**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menú contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="4ef47-256">Seleccione **ASP.NET Core** y **Clase de controlador de MVC**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="4ef47-257">Asigne al controlador el nombre **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="4ef47-257">Name the controller **HelloWorldController**.</span></span>

![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="4ef47-259">Reemplace el contenido de *Controllers/HelloWorldController.cs* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4ef47-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="4ef47-260">Cada método `public` en un controlador puede ser invocado como un punto de conexión HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef47-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="4ef47-261">En el ejemplo anterior, ambos métodos devuelven una cadena.</span><span class="sxs-lookup"><span data-stu-id="4ef47-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="4ef47-262">Observe los comentarios delante de cada método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="4ef47-263">Un extremo HTTP es una dirección URL que se puede usar como destino en la aplicación web, como por ejemplo `https://localhost:5001/HelloWorld`. Combina el protocolo usado `HTTPS`, la ubicación de red del servidor web (incluido el puerto TCP) `localhost:5001` y el URI de destino `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="4ef47-264">El primer comentario indica que se trata de un método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) que se invoca anexando `/HelloWorld/` a la dirección URL base.</span><span class="sxs-lookup"><span data-stu-id="4ef47-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="4ef47-265">El segundo comentario especifica un método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) que se invoca anexando `/HelloWorld/Welcome/` a la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="4ef47-266">Más adelante en el tutorial se usa el motor de scaffolding para generar métodos `HTTP POST` que actualizan los datos.</span><span class="sxs-lookup"><span data-stu-id="4ef47-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="4ef47-267">Ejecute la aplicación en modo de no depuración y anexione "HelloWorld" a la ruta de acceso en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ef47-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="4ef47-268">El método `Index` devuelve una cadena.</span><span class="sxs-lookup"><span data-stu-id="4ef47-268">The `Index` method returns a string.</span></span>

![Ventana del explorador que muestra una respuesta de la aplicación a "This is my default action" (Esta es mi acción predeterminada)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="4ef47-270">MVC invoca las clases del controlador (y los métodos de acción que contienen) en función de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="4ef47-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="4ef47-271">La [lógica de enrutamiento de URL](xref:mvc/controllers/routing) predeterminada que usa MVC emplea un formato similar al siguiente para determinar qué código se debe invocar:</span><span class="sxs-lookup"><span data-stu-id="4ef47-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="4ef47-272">El formato de enrutamiento se establece en el método `Configure` del archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4ef47-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="4ef47-273">Cuando se navega a la aplicación y no se suministra ningún segmento de dirección URL, de manera predeterminada se usan el controlador "Home" y el método "Index" especificados en la línea de plantilla resaltada arriba.</span><span class="sxs-lookup"><span data-stu-id="4ef47-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="4ef47-274">El primer segmento de dirección URL determina la clase de controlador que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="4ef47-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="4ef47-275">De modo que `localhost:{PORT}/HelloWorld` se asigna a la clase `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="4ef47-276">La segunda parte del segmento de dirección URL determina el método de acción en la clase.</span><span class="sxs-lookup"><span data-stu-id="4ef47-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="4ef47-277">De modo que `localhost:{PORT}/HelloWorld/Index` podría provocar que se ejecute el método `Index` de la clase `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="4ef47-278">Tenga en cuenta que solo es necesario navegar a `localhost:{PORT}/HelloWorld` para que se llame al método `Index` de manera predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4ef47-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="4ef47-279">Esto es porque `Index` es el método predeterminado al que se llamará en un controlador si no se especifica explícitamente un nombre de método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="4ef47-280">La tercera parte del segmento de dirección URL (`id`) es para los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="4ef47-281">Los datos de ruta se explican más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4ef47-282">Vaya a `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="4ef47-283">El método `Welcome` se ejecuta y devuelve la cadena `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="4ef47-284">Para esta dirección URL, el controlador es `HelloWorld` y `Welcome` es el método de acción.</span><span class="sxs-lookup"><span data-stu-id="4ef47-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="4ef47-285">Todavía no ha usado el elemento `[Parameters]` de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Ventana del explorador que muestra la respuesta de la aplicación "This is the Welcome action method" (Este es el método de acción predeterminado)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="4ef47-287">Modifique el código para pasar cierta información del parámetro desde la dirección URL al controlador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="4ef47-288">Por ejemplo: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="4ef47-289">Cambie el método `Welcome` para que incluya dos parámetros, como se muestra en el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="4ef47-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="4ef47-290">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4ef47-290">The preceding code:</span></span>

* <span data-ttu-id="4ef47-291">Usa la característica de parámetro opcional de C# para indicar que el parámetro `numTimes` tiene el valor predeterminado 1 si no se pasa ningún valor para ese parámetro.</span><span class="sxs-lookup"><span data-stu-id="4ef47-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="4ef47-292">Usa `HtmlEncoder.Default.Encode` para proteger la aplicación de entradas malintencionadas (en concreto JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4ef47-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="4ef47-293">Usa [cadenas interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) en `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="4ef47-294">Ejecute la aplicación y navegue a:</span><span class="sxs-lookup"><span data-stu-id="4ef47-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="4ef47-295">Reemplace `{PORT}` por el número de puerto. Puede probar distintos valores para `name` y `numtimes` en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4ef47-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="4ef47-296">El sistema de [enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre de la cadena de consulta en la barra de direcciones a los parámetros del método.</span><span class="sxs-lookup"><span data-stu-id="4ef47-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="4ef47-297">Vea [Model Binding](xref:mvc/models/model-binding) (Enlace de modelos) para más información.</span><span class="sxs-lookup"><span data-stu-id="4ef47-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Ventana del explorador que muestra una respuesta de la aplicación de Hello Rick, NumTimes es\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="4ef47-299">En la imagen anterior, el segmento de dirección URL (`Parameters`) no se usa y los parámetros `name` y `numTimes` se pasan en la [cadena de consulta](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="4ef47-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="4ef47-300">El `?` (signo de interrogación) en la dirección URL anterior es un separador y le sigue la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4ef47-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="4ef47-301">El carácter `&` separa los pares campo-valor.</span><span class="sxs-lookup"><span data-stu-id="4ef47-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="4ef47-302">Reemplace el método `Welcome` con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4ef47-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="4ef47-303">Ejecute la aplicación y escriba la siguiente dirección URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="4ef47-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="4ef47-304">Esta vez el tercer segmento de dirección URL coincide con el parámetro de ruta `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="4ef47-305">El método `Welcome` contiene un parámetro `id` que coincide con la plantilla de dirección URL en el método `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="4ef47-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="4ef47-306">El elemento `?` final (en `id?`) indica que el parámetro `id` es opcional.</span><span class="sxs-lookup"><span data-stu-id="4ef47-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="4ef47-307">En estos ejemplos, el controlador ha realizado la parte "VC" de MVC, es decir, el trabajo de vista y de controlador.</span><span class="sxs-lookup"><span data-stu-id="4ef47-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="4ef47-308">El controlador devuelve HTML directamente.</span><span class="sxs-lookup"><span data-stu-id="4ef47-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="4ef47-309">Por lo general, no es aconsejable que los controles devuelvan HTML directamente, porque resulta muy complicado de programar y mantener.</span><span class="sxs-lookup"><span data-stu-id="4ef47-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="4ef47-310">En su lugar, se suele usar un archivo de plantilla de vista de Razor independiente para ayudar a generar la respuesta HTML.</span><span class="sxs-lookup"><span data-stu-id="4ef47-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="4ef47-311">Haremos esto en el siguiente tutorial.</span><span class="sxs-lookup"><span data-stu-id="4ef47-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4ef47-312">[Anterior](start-mvc.md)
> [Siguiente](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="4ef47-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
