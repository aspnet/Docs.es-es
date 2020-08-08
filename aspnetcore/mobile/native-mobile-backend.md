---
title: Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo crear servicios back-end con ASP.NET Core MVC que admitan aplicaciones móviles nativas.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 75943a15878634568830db74f895653571c54d0f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019435"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="d961f-103">Crear servicios back-end para aplicaciones móviles nativas con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d961f-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="d961f-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d961f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d961f-105">Las aplicaciones móviles pueden comunicarse con servicios back-end de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d961f-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="d961f-106">Para obtener instrucciones sobre cómo conectar servicios web locales desde simuladores de iOS y emuladores de Android, vea [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services) (Conexión a servicios web locales desde simuladores de iOS y emuladores de Android).</span><span class="sxs-lookup"><span data-stu-id="d961f-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="d961f-107">Ver o descargar código de ejemplo de servicios back-end</span><span class="sxs-lookup"><span data-stu-id="d961f-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="d961f-108">La aplicación móvil nativa de ejemplo</span><span class="sxs-lookup"><span data-stu-id="d961f-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="d961f-109">Este tutorial muestra cómo crear servicios back-end mediante ASP.NET Core MVC para admitir aplicaciones móviles nativas.</span><span class="sxs-lookup"><span data-stu-id="d961f-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="d961f-110">Usa la [aplicación Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como su cliente nativo, lo que incluye clientes nativos independientes para dispositivos Android, iOS, Windows Universal y Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="d961f-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="d961f-111">Puede seguir el tutorial vinculado para crear la aplicación nativa (e instalar las herramientas de Xamarin gratuitas necesarias), así como descargar la solución de ejemplo de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="d961f-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="d961f-112">El ejemplo de Xamarin incluye un proyecto de servicios de ASP.NET Web API 2, que sustituye a las aplicaciones de ASP.NET Core de este artículo (sin cambios requeridos por el cliente).</span><span class="sxs-lookup"><span data-stu-id="d961f-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aplicación ToDoRest que se ejecuta en un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="d961f-114">Características</span><span class="sxs-lookup"><span data-stu-id="d961f-114">Features</span></span>

<span data-ttu-id="d961f-115">La aplicación ToDoRest permite enumerar, agregar, eliminar y actualizar tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="d961f-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="d961f-116">Cada tarea tiene un identificador, un nombre, notas y una propiedad que indica si ya se ha realizado.</span><span class="sxs-lookup"><span data-stu-id="d961f-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="d961f-117">La vista principal de las tareas, como se muestra anteriormente, indica el nombre de cada tarea e indica si se ha realizado con una marca de verificación.</span><span class="sxs-lookup"><span data-stu-id="d961f-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="d961f-118">Al pulsar el icono `+` se abre un cuadro de diálogo para agregar un elemento:</span><span class="sxs-lookup"><span data-stu-id="d961f-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Cuadro de diálogo para agregar un elemento](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="d961f-120">Al pulsar un elemento en la pantalla de la lista principal se abre un cuadro de diálogo de edición, donde se puede modificar el nombre del elemento, las notas y la configuración de Done (Listo), o se puede eliminar el elemento:</span><span class="sxs-lookup"><span data-stu-id="d961f-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Cuadro de diálogo de edición del elemento](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="d961f-122">Este ejemplo está configurado para usar de forma predeterminada servicios back-end hospedados en developer.xamarin.com, lo que permite operaciones de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="d961f-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="d961f-123">Para probarlo usted mismo con la aplicación de ASP.NET Core que creó en la siguiente sección que se ejecuta en el equipo, debe actualizar la constante `RestUrl` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d961f-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="d961f-124">Vaya hasta el proyecto `ToDoREST` y abra el archivo *Constants.cs*.</span><span class="sxs-lookup"><span data-stu-id="d961f-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="d961f-125">Reemplace `RestUrl` con una dirección URL que incluya la dirección IP de su equipo (no localhost ni 127.0.0.1, puesto que esta dirección se usa desde el emulador de dispositivo, no desde el equipo).</span><span class="sxs-lookup"><span data-stu-id="d961f-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="d961f-126">Incluya también el número de puerto (5000).</span><span class="sxs-lookup"><span data-stu-id="d961f-126">Include the port number as well (5000).</span></span> <span data-ttu-id="d961f-127">Para comprobar que los servicios funcionan con un dispositivo, asegúrese de que no tiene un firewall activo que bloquea el acceso a este puerto.</span><span class="sxs-lookup"><span data-stu-id="d961f-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="d961f-128">Creación del proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d961f-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="d961f-129">Cree una aplicación web de ASP.NET Core en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d961f-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="d961f-130">Elija la plantilla de API web y Sin autenticación.</span><span class="sxs-lookup"><span data-stu-id="d961f-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="d961f-131">Denomine el proyecto *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="d961f-131">Name the project *ToDoApi*.</span></span>

![Cuadro de diálogo Nueva aplicación web ASP.NET con la plantilla de proyecto API web seleccionada](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="d961f-133">La aplicación debería responder a todas las solicitudes realizadas al puerto 5000.</span><span class="sxs-lookup"><span data-stu-id="d961f-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="d961f-134">Actualice *Program.cs* para que incluya `.UseUrls("http://*:5000")` para conseguir lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d961f-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="d961f-135">Asegúrese de que ejecuta la aplicación directamente, en lugar de tras IIS Express, que omite las solicitudes no locales de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d961f-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="d961f-136">Ejecute [dotnet run](/dotnet/core/tools/dotnet-run) desde un símbolo del sistema o elija el perfil del nombre de aplicación en la lista desplegable de destino de depuración en la barra de herramientas de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d961f-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="d961f-137">Agregue una clase de modelo para representar las tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="d961f-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="d961f-138">Marque los campos obligatorios mediante el atributo `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="d961f-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="d961f-139">Los métodos de API necesitan alguna manera de trabajar con los datos.</span><span class="sxs-lookup"><span data-stu-id="d961f-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="d961f-140">Use la misma interfaz de `IToDoRepository` que usa el ejemplo original de Xamarin:</span><span class="sxs-lookup"><span data-stu-id="d961f-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="d961f-141">En este ejemplo, la implementación usa solo una colección de elementos privada:</span><span class="sxs-lookup"><span data-stu-id="d961f-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="d961f-142">Configure la implementación en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d961f-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="d961f-143">En este punto, está listo para crear el *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="d961f-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="d961f-144">Obtenga más información sobre cómo crear API web en [Cree su primera API web con ASP.NET Core MVC y Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="d961f-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="d961f-145">Crear el controlador</span><span class="sxs-lookup"><span data-stu-id="d961f-145">Creating the Controller</span></span>

<span data-ttu-id="d961f-146">Agregue un nuevo controlador para el proyecto: *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="d961f-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="d961f-147">Debe heredar de Microsoft.AspNetCore.Mvc.Controller.</span><span class="sxs-lookup"><span data-stu-id="d961f-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="d961f-148">Agregue un atributo `Route` para indicar que el controlador controlará las solicitudes realizadas a las rutas de acceso que comiencen con `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="d961f-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="d961f-149">El token `[controller]` de la ruta se sustituye por el nombre del controlador (si se omite el sufijo `Controller`) y es especialmente útil para las rutas globales.</span><span class="sxs-lookup"><span data-stu-id="d961f-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="d961f-150">Obtenga más información sobre el [enrutamiento](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="d961f-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="d961f-151">El controlador necesita un `IToDoRepository` para funcionar. Solicite una instancia de este tipo a través del constructor del controlador.</span><span class="sxs-lookup"><span data-stu-id="d961f-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="d961f-152">En tiempo de ejecución, esta instancia se proporcionará con la compatibilidad del marco con la [inserción de dependencias](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="d961f-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="d961f-153">Esta API es compatible con cuatro verbos HTTP diferentes para realizar operaciones CRUD (creación, lectura, actualización, eliminación) en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="d961f-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="d961f-154">La más simple de ellas es la operación de lectura, que corresponde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="d961f-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="d961f-155">Leer elementos</span><span class="sxs-lookup"><span data-stu-id="d961f-155">Reading Items</span></span>

<span data-ttu-id="d961f-156">La solicitud de una lista de elementos se realiza con una solicitud GET al método `List`.</span><span class="sxs-lookup"><span data-stu-id="d961f-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="d961f-157">El atributo `[HttpGet]` en el método `List` indica que esta acción solo debe controlar las solicitudes GET.</span><span class="sxs-lookup"><span data-stu-id="d961f-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="d961f-158">La ruta de esta acción es la ruta especificada en el controlador.</span><span class="sxs-lookup"><span data-stu-id="d961f-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="d961f-159">No es necesario usar el nombre de acción como parte de la ruta.</span><span class="sxs-lookup"><span data-stu-id="d961f-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="d961f-160">Solo debe asegurarse de que cada acción tiene una ruta única e inequívoca.</span><span class="sxs-lookup"><span data-stu-id="d961f-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="d961f-161">El enrutamiento de atributos se puede aplicar tanto a los niveles de controlador como de método para crear rutas específicas.</span><span class="sxs-lookup"><span data-stu-id="d961f-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="d961f-162">El método `List` devuelve un código de respuesta 200 OK y todos los elementos de lista de tareas, serializados como JSON.</span><span class="sxs-lookup"><span data-stu-id="d961f-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="d961f-163">Puede probar el nuevo método de API con una variedad de herramientas, como [Postman](https://www.getpostman.com/docs/), que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="d961f-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Consola de Postman que muestra una solicitud GET para las tareas pendientes y el cuerpo de la respuesta que muestra el JSON de los tres elementos devueltos](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="d961f-165">Crear elementos</span><span class="sxs-lookup"><span data-stu-id="d961f-165">Creating Items</span></span>

<span data-ttu-id="d961f-166">Por convención, la creación de elementos de datos se asigna al verbo HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="d961f-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="d961f-167">El método `Create` tiene un atributo `[HttpPost]` aplicado y acepta una instancia `ToDoItem`.</span><span class="sxs-lookup"><span data-stu-id="d961f-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="d961f-168">Puesto que el argumento `item` se pasa en el cuerpo de la solicitud POST, este parámetro especifica el atributo `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="d961f-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="d961f-169">Dentro del método, se comprueba la validez del elemento y si existió anteriormente en el almacén de datos y, si no hay problemas, se agrega mediante el repositorio.</span><span class="sxs-lookup"><span data-stu-id="d961f-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="d961f-170">Al comprobar `ModelState.IsValid` se realiza una [validación de modelos](../mvc/models/validation.md), y debe realizarse en cada método de API que acepte datos proporcionados por usuario.</span><span class="sxs-lookup"><span data-stu-id="d961f-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="d961f-171">El ejemplo usa una enumeración que contiene códigos de error que se pasan al cliente móvil:</span><span class="sxs-lookup"><span data-stu-id="d961f-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="d961f-172">Pruebe a agregar nuevos elementos con Postman eligiendo el verbo POST que proporciona el nuevo objeto en formato JSON en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d961f-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="d961f-173">También debe agregar un encabezado de solicitud que especifica un `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="d961f-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Consola Postman que muestra una solicitud POST y una respuesta](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="d961f-175">El método devuelve el elemento recién creado en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d961f-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="d961f-176">Actualizar elementos</span><span class="sxs-lookup"><span data-stu-id="d961f-176">Updating Items</span></span>

<span data-ttu-id="d961f-177">La modificación de registros se realiza mediante solicitudes HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d961f-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="d961f-178">Aparte de este cambio, el método `Edit` es casi idéntico a `Create`.</span><span class="sxs-lookup"><span data-stu-id="d961f-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="d961f-179">Tenga en cuenta que, si no se encuentra el registro, la acción `Edit` devolverá una respuesta `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="d961f-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="d961f-180">Para probar con Postman, cambie el verbo a PUT.</span><span class="sxs-lookup"><span data-stu-id="d961f-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="d961f-181">Especifique los datos actualizados del objeto en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d961f-181">Specify the updated object data in the Body of the request.</span></span>

![Consola Postman que muestra una solicitud PUT y una respuesta](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="d961f-183">Este método devuelve una respuesta `NoContent` (204) cuando se realiza correctamente, para mantener la coherencia con la API existente.</span><span class="sxs-lookup"><span data-stu-id="d961f-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="d961f-184">Eliminar elementos</span><span class="sxs-lookup"><span data-stu-id="d961f-184">Deleting Items</span></span>

<span data-ttu-id="d961f-185">La eliminación de registros se consigue mediante solicitudes DELETE al servicio y pasando el identificador del elemento que va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="d961f-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="d961f-186">Al igual que con las actualizaciones, las solicitudes para elementos que no existen recibirán respuestas `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="d961f-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="d961f-187">De lo contrario, las solicitudes correctas recibirán una respuesta `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="d961f-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="d961f-188">Tenga en cuenta que, al probar la funcionalidad de eliminar, no se necesita nada en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d961f-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Consola Postman que muestra una solicitud DELETE y una respuesta](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="d961f-190">Convenciones comunes de Web API</span><span class="sxs-lookup"><span data-stu-id="d961f-190">Common Web API Conventions</span></span>

<span data-ttu-id="d961f-191">Al desarrollar los servicios back-end de la aplicación, necesitará acceder a un conjunto coherente de convenciones o directivas para controlar cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="d961f-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="d961f-192">Por ejemplo, en el servicio mostrado anteriormente, las solicitudes de registros específicos que no se encontraron recibieron una respuesta `NotFound`, en lugar de una respuesta `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="d961f-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="d961f-193">De forma similar, los comandos realizados a este servicio que pasaron en tipos enlazados a un modelo siempre se comprobaron como `ModelState.IsValid` y devolvieron una `BadRequest` para los tipos de modelos no válidos.</span><span class="sxs-lookup"><span data-stu-id="d961f-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="d961f-194">Después de identificar una directiva común para las API, normalmente puede encapsularla en un [filtro](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="d961f-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="d961f-195">Obtenga más información sobre [cómo encapsular directivas de API comunes en aplicaciones de ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="d961f-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d961f-196">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d961f-196">Additional resources</span></span>

* [<span data-ttu-id="d961f-197">Autenticación y autorización</span><span class="sxs-lookup"><span data-stu-id="d961f-197">Authentication and Authorization</span></span>](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
