---
title: Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear servicios back-end con ASP.NET Core MVC que admitan aplicaciones móviles nativas.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564032"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="6fe48-103">Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fe48-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="6fe48-104">Por [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="6fe48-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="6fe48-105">Las aplicaciones móviles pueden comunicarse con servicios back-end de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fe48-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="6fe48-106">Para obtener instrucciones sobre cómo conectar servicios web locales desde simuladores de iOS y emuladores de Android, vea [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services) (Conexión a servicios web locales desde simuladores de iOS y emuladores de Android).</span><span class="sxs-lookup"><span data-stu-id="6fe48-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="6fe48-107">Ver o descargar código de ejemplo de servicios back-end</span><span class="sxs-lookup"><span data-stu-id="6fe48-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="6fe48-108">La aplicación móvil nativa de ejemplo</span><span class="sxs-lookup"><span data-stu-id="6fe48-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="6fe48-109">En este tutorial se muestra cómo crear servicios back-end mediante ASP.NET Core para admitir aplicaciones móviles nativas.</span><span class="sxs-lookup"><span data-stu-id="6fe48-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="6fe48-110">Usa la [aplicación Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como su cliente nativo, que incluye clientes nativos independientes para Android, iOS y Windows.</span><span class="sxs-lookup"><span data-stu-id="6fe48-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="6fe48-111">Puede seguir el tutorial vinculado para crear la aplicación nativa (e instalar las herramientas de Xamarin gratuitas necesarias), así como descargar la solución de ejemplo de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6fe48-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="6fe48-112">El ejemplo de Xamarin incluye un ASP.NET Core proyecto de servicios de API Web, que reemplaza la aplicación ASP.NET Core de este artículo (sin necesidad de realizar cambios en el cliente).</span><span class="sxs-lookup"><span data-stu-id="6fe48-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aplicación ToDoRest que se ejecuta en un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="6fe48-114">Características</span><span class="sxs-lookup"><span data-stu-id="6fe48-114">Features</span></span>

<span data-ttu-id="6fe48-115">La [aplicación TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) permite enumerar, agregar, eliminar y actualizar elementos To-Do.</span><span class="sxs-lookup"><span data-stu-id="6fe48-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="6fe48-116">Cada tarea tiene un identificador, un nombre, notas y una propiedad que indica si ya se ha realizado.</span><span class="sxs-lookup"><span data-stu-id="6fe48-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="6fe48-117">La vista principal de las tareas, como se muestra anteriormente, indica el nombre de cada tarea e indica si se ha realizado con una marca de verificación.</span><span class="sxs-lookup"><span data-stu-id="6fe48-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="6fe48-118">Al pulsar el icono `+` se abre un cuadro de diálogo para agregar un elemento:</span><span class="sxs-lookup"><span data-stu-id="6fe48-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Cuadro de diálogo para agregar un elemento](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="6fe48-120">Al pulsar un elemento en la pantalla de la lista principal se abre un cuadro de diálogo de edición, donde se puede modificar el nombre del elemento, las notas y la configuración de Done (Listo), o se puede eliminar el elemento:</span><span class="sxs-lookup"><span data-stu-id="6fe48-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Cuadro de diálogo de edición del elemento](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="6fe48-122">Para probarlo usted mismo con la aplicación ASP.NET Core creada en la siguiente sección, que se ejecuta en el equipo, actualice la constante de la aplicación [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .</span><span class="sxs-lookup"><span data-stu-id="6fe48-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="6fe48-123">Los emuladores de Android no se ejecutan en el equipo local y usan una dirección IP de bucle invertido (10.0.2.2) para comunicarse con el equipo local.</span><span class="sxs-lookup"><span data-stu-id="6fe48-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="6fe48-124">Aproveche [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) para detectar el funcionamiento del sistema para usar la dirección URL correcta.</span><span class="sxs-lookup"><span data-stu-id="6fe48-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="6fe48-125">Navegue hasta el [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) proyecto y abra el [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) archivo.</span><span class="sxs-lookup"><span data-stu-id="6fe48-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="6fe48-126">El archivo *constants.CS* contiene la configuración siguiente.</span><span class="sxs-lookup"><span data-stu-id="6fe48-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="6fe48-127">Opcionalmente, puede implementar el servicio Web en un servicio en la nube como Azure y actualizar el `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="6fe48-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="6fe48-128">Creación del proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fe48-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="6fe48-129">Cree una aplicación web de ASP.NET Core en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6fe48-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="6fe48-130">Elija la plantilla API Web.</span><span class="sxs-lookup"><span data-stu-id="6fe48-130">Choose the Web API template.</span></span> <span data-ttu-id="6fe48-131">Asigne al proyecto el nombre *TodoAPI*.</span><span class="sxs-lookup"><span data-stu-id="6fe48-131">Name the project *TodoAPI*.</span></span>

![Cuadro de diálogo Nueva aplicación web ASP.NET con la plantilla de proyecto API web seleccionada](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="6fe48-133">La aplicación debe responder a todas las solicitudes realizadas en el puerto 5000, incluido el tráfico HTTP de texto no cifrado para nuestro cliente móvil.</span><span class="sxs-lookup"><span data-stu-id="6fe48-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="6fe48-134">Actualice *Startup.CS* de modo que <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> no se ejecute en el desarrollo:</span><span class="sxs-lookup"><span data-stu-id="6fe48-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="6fe48-135">Ejecute la aplicación directamente, en lugar de detrás IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6fe48-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="6fe48-136">IIS Express omite las solicitudes no locales de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6fe48-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="6fe48-137">Ejecute [dotnet Run](/dotnet/core/tools/dotnet-run) desde un símbolo del sistema o elija el perfil de nombre de la aplicación en la lista desplegable destino de depuración de la barra de herramientas de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6fe48-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="6fe48-138">Agregue una clase de modelo para representar las tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="6fe48-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="6fe48-139">Marque los campos obligatorios mediante el atributo `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="6fe48-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="6fe48-140">Los métodos de API necesitan alguna manera de trabajar con los datos.</span><span class="sxs-lookup"><span data-stu-id="6fe48-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="6fe48-141">Use la misma interfaz de `ITodoRepository` que usa el ejemplo original de Xamarin:</span><span class="sxs-lookup"><span data-stu-id="6fe48-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="6fe48-142">En este ejemplo, la implementación usa solo una colección de elementos privada:</span><span class="sxs-lookup"><span data-stu-id="6fe48-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="6fe48-143">Configure la implementación en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6fe48-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="6fe48-144">Crear el controlador</span><span class="sxs-lookup"><span data-stu-id="6fe48-144">Creating the Controller</span></span>

<span data-ttu-id="6fe48-145">Agregue un nuevo controlador al proyecto, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span><span class="sxs-lookup"><span data-stu-id="6fe48-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="6fe48-146">Debe heredar de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="6fe48-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="6fe48-147">Agregue un atributo `Route` para indicar que el controlador controlará las solicitudes realizadas a las rutas de acceso que comiencen con `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="6fe48-148">El token `[controller]` de la ruta se sustituye por el nombre del controlador (si se omite el sufijo `Controller`) y es especialmente útil para las rutas globales.</span><span class="sxs-lookup"><span data-stu-id="6fe48-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="6fe48-149">Obtenga más información sobre el [enrutamiento](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="6fe48-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="6fe48-150">El controlador necesita un `ITodoRepository` para funcionar. Solicite una instancia de este tipo a través del constructor del controlador.</span><span class="sxs-lookup"><span data-stu-id="6fe48-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="6fe48-151">En tiempo de ejecución, esta instancia se proporcionará con la compatibilidad del marco con la [inserción de dependencias](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="6fe48-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="6fe48-152">Esta API es compatible con cuatro verbos HTTP diferentes para realizar operaciones CRUD (creación, lectura, actualización, eliminación) en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="6fe48-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="6fe48-153">La más simple de ellas es la operación de lectura, que corresponde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6fe48-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="6fe48-154">Leer elementos</span><span class="sxs-lookup"><span data-stu-id="6fe48-154">Reading Items</span></span>

<span data-ttu-id="6fe48-155">La solicitud de una lista de elementos se realiza con una solicitud GET al método `List`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="6fe48-156">El atributo `[HttpGet]` en el método `List` indica que esta acción solo debe controlar las solicitudes GET.</span><span class="sxs-lookup"><span data-stu-id="6fe48-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="6fe48-157">La ruta de esta acción es la ruta especificada en el controlador.</span><span class="sxs-lookup"><span data-stu-id="6fe48-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="6fe48-158">No es necesario usar el nombre de acción como parte de la ruta.</span><span class="sxs-lookup"><span data-stu-id="6fe48-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="6fe48-159">Solo debe asegurarse de que cada acción tiene una ruta única e inequívoca.</span><span class="sxs-lookup"><span data-stu-id="6fe48-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="6fe48-160">El enrutamiento de atributos se puede aplicar tanto a los niveles de controlador como de método para crear rutas específicas.</span><span class="sxs-lookup"><span data-stu-id="6fe48-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="6fe48-161">El `List` método devuelve un código de respuesta 200 aceptar y todos los elementos de la lista de tareas, serializados como JSON.</span><span class="sxs-lookup"><span data-stu-id="6fe48-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="6fe48-162">Puede probar el nuevo método de API con una variedad de herramientas, como [Postman](https://www.getpostman.com/docs/), que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="6fe48-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Consola de Postman que muestra una solicitud GET para las tareas pendientes y el cuerpo de la respuesta que muestra el JSON de los tres elementos devueltos](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="6fe48-164">Crear elementos</span><span class="sxs-lookup"><span data-stu-id="6fe48-164">Creating Items</span></span>

<span data-ttu-id="6fe48-165">Por convención, la creación de elementos de datos se asigna al verbo HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="6fe48-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="6fe48-166">El método `Create` tiene un atributo `[HttpPost]` aplicado y acepta una instancia `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="6fe48-167">Puesto que el argumento `item` se pasa en el cuerpo de la solicitud POST, este parámetro especifica el atributo `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="6fe48-168">Dentro del método, se comprueba la validez del elemento y si existió anteriormente en el almacén de datos y, si no hay problemas, se agrega mediante el repositorio.</span><span class="sxs-lookup"><span data-stu-id="6fe48-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="6fe48-169">Al comprobar `ModelState.IsValid` se realiza una [validación de modelos](../mvc/models/validation.md), y debe realizarse en cada método de API que acepte datos proporcionados por usuario.</span><span class="sxs-lookup"><span data-stu-id="6fe48-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="6fe48-170">En el ejemplo se usa un que `enum` contiene códigos de error que se pasan al cliente móvil:</span><span class="sxs-lookup"><span data-stu-id="6fe48-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="6fe48-171">Pruebe a agregar nuevos elementos con Postman eligiendo el verbo POST que proporciona el nuevo objeto en formato JSON en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6fe48-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="6fe48-172">También debe agregar un encabezado de solicitud que especifica un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Consola Postman que muestra una solicitud POST y una respuesta](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="6fe48-174">El método devuelve el elemento recién creado en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="6fe48-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="6fe48-175">Actualizar elementos</span><span class="sxs-lookup"><span data-stu-id="6fe48-175">Updating Items</span></span>

<span data-ttu-id="6fe48-176">La modificación de registros se realiza mediante solicitudes HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6fe48-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="6fe48-177">Aparte de este cambio, el método `Edit` es casi idéntico a `Create`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="6fe48-178">Tenga en cuenta que, si no se encuentra el registro, la acción `Edit` devolverá una respuesta `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="6fe48-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="6fe48-179">Para probar con Postman, cambie el verbo a PUT.</span><span class="sxs-lookup"><span data-stu-id="6fe48-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="6fe48-180">Especifique los datos actualizados del objeto en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6fe48-180">Specify the updated object data in the Body of the request.</span></span>

![Consola Postman que muestra una solicitud PUT y una respuesta](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="6fe48-182">Este método devuelve una respuesta `NoContent` (204) cuando se realiza correctamente, para mantener la coherencia con la API existente.</span><span class="sxs-lookup"><span data-stu-id="6fe48-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="6fe48-183">Eliminar elementos</span><span class="sxs-lookup"><span data-stu-id="6fe48-183">Deleting Items</span></span>

<span data-ttu-id="6fe48-184">La eliminación de registros se consigue mediante solicitudes DELETE al servicio y pasando el identificador del elemento que va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="6fe48-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="6fe48-185">Al igual que con las actualizaciones, las solicitudes para elementos que no existen recibirán respuestas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="6fe48-186">De lo contrario, las solicitudes correctas recibirán una respuesta `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="6fe48-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="6fe48-187">Tenga en cuenta que, al probar la funcionalidad de eliminar, no se necesita nada en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="6fe48-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Consola Postman que muestra una solicitud DELETE y una respuesta](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="6fe48-189">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="6fe48-189">Prevent over-posting</span></span>

<span data-ttu-id="6fe48-190">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="6fe48-191">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="6fe48-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="6fe48-192">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="6fe48-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="6fe48-193">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="6fe48-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="6fe48-194">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="6fe48-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="6fe48-195">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="6fe48-195">A DTO may be used to:</span></span>

* <span data-ttu-id="6fe48-196">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="6fe48-196">Prevent over-posting.</span></span>
* <span data-ttu-id="6fe48-197">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="6fe48-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="6fe48-198">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="6fe48-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="6fe48-199">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="6fe48-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="6fe48-200">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="6fe48-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="6fe48-201">Para demostrar el enfoque DTO, consulte [evitar la sobreexposición](xref:tutorials/first-web-api#prevent-over-posting) .</span><span class="sxs-lookup"><span data-stu-id="6fe48-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="6fe48-202">Convenciones comunes de Web API</span><span class="sxs-lookup"><span data-stu-id="6fe48-202">Common Web API Conventions</span></span>

<span data-ttu-id="6fe48-203">Al desarrollar los servicios back-end de la aplicación, necesitará acceder a un conjunto coherente de convenciones o directivas para controlar cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="6fe48-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="6fe48-204">Por ejemplo, en el servicio mostrado anteriormente, las solicitudes de registros específicos que no se encontraron recibieron una respuesta `NotFound`, en lugar de una respuesta `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="6fe48-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="6fe48-205">De forma similar, los comandos realizados a este servicio que pasaron en tipos enlazados a un modelo siempre se comprobaron como `ModelState.IsValid` y devolvieron una `BadRequest` para los tipos de modelos no válidos.</span><span class="sxs-lookup"><span data-stu-id="6fe48-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="6fe48-206">Después de identificar una directiva común para las API, normalmente puede encapsularla en un [filtro](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="6fe48-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="6fe48-207">Obtenga más información sobre [cómo encapsular directivas de API comunes en aplicaciones de ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="6fe48-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6fe48-208">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6fe48-208">Additional resources</span></span>

- [<span data-ttu-id="6fe48-209">Xamarin. Forms: autenticación de servicio Web</span><span class="sxs-lookup"><span data-stu-id="6fe48-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="6fe48-210">Xamarin. Forms: consumo de un servicio web RESTful</span><span class="sxs-lookup"><span data-stu-id="6fe48-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="6fe48-211">Microsoft Learn: consumir servicios web REST en aplicaciones Xamarin</span><span class="sxs-lookup"><span data-stu-id="6fe48-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="6fe48-212">Microsoft Learn: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fe48-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
