---
title: 'Tutorial: Llamada a una API web de ASP.NET Core con JavaScript'
author: rick-anderson
description: Obtenga información sobre cómo llamar a una API web de ASP.NET Core con JavaScript.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703714"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="d1568-103">Tutorial: Llamada a una API web de ASP.NET Core con JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1568-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="d1568-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1568-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1568-105">En este tutorial se muestra cómo llamar a una API web de ASP.NET Core con JavaScript mediante la [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="d1568-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1568-106">Para ASP.NET Core 2.2, consulte la versión 2.2 de [Llamada a la API web con JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="d1568-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="d1568-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d1568-107">Prerequisites</span></span>

* <span data-ttu-id="d1568-108">Finalización del [Tutorial: Creación de una API web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="d1568-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="d1568-109">Familiaridad con CSS, HTML y JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1568-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d1568-110">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="d1568-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="d1568-111">En esta sección, agregará una página HTML que contiene formularios para crear y administrar las tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="d1568-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="d1568-112">Los controladores de eventos se asocian a los elementos de la página.</span><span class="sxs-lookup"><span data-stu-id="d1568-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="d1568-113">Los controladores de eventos producen solicitudes HTTP a los métodos de acción de la API web.</span><span class="sxs-lookup"><span data-stu-id="d1568-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="d1568-114">La función `fetch` de la API Fetch inicia cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1568-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="d1568-115">La función `fetch` devuelve un objeto [Promise`Response`, que contiene una respuesta HTTP representada como objeto ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="d1568-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="d1568-116">Un patrón común es extraer el cuerpo de la respuesta JSON invocando la función `json` en el `Response` objeto.</span><span class="sxs-lookup"><span data-stu-id="d1568-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="d1568-117">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="d1568-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="d1568-118">La llamada `fetch` más simple acepta un único parámetro que representa la ruta.</span><span class="sxs-lookup"><span data-stu-id="d1568-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="d1568-119">Un segundo parámetro, conocido como el objeto `init`, es opcional.</span><span class="sxs-lookup"><span data-stu-id="d1568-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="d1568-120">`init` se utiliza para configurar la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1568-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="d1568-121">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d1568-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d1568-122">El siguiente código resaltado es necesario en el método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1568-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="d1568-123">Cree una carpeta *wwwroot* en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d1568-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="d1568-124">Cree una carpeta *js* dentro de la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d1568-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="d1568-125">Agregue un archivo HTML denominado *index.html* a la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d1568-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="d1568-126">Reemplace el contenido de *index.html* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="d1568-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="d1568-127">Agregue un archivo CSS denominado *site.css* a la carpeta *wwwroot/css*.</span><span class="sxs-lookup"><span data-stu-id="d1568-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="d1568-128">Reemplace el contenido de *site.css* por los estilos siguientes:</span><span class="sxs-lookup"><span data-stu-id="d1568-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="d1568-129">Agregue un archivo de JavaScript denominado *site.js*, a la carpeta *wwwroot/js*.</span><span class="sxs-lookup"><span data-stu-id="d1568-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="d1568-130">Reemplace el contenido de *site.js* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d1568-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d1568-131">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="d1568-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="d1568-132">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d1568-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="d1568-133">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d1568-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d1568-134">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="d1568-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="d1568-135">A continuación, encontrará algunas explicaciones de las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="d1568-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="d1568-136">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="d1568-136">Get a list of to-do items</span></span>

<span data-ttu-id="d1568-137">En el código siguiente, se envía una solicitud HTTP GET a la ruta *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="d1568-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="d1568-138">Cuando la API web devuelve un código de estado correcto, se invoca la función `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="d1568-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="d1568-139">Cada tarea pendiente en el parámetro de matriz aceptado por `_displayItems` se agrega a una tabla con los botones **Editar** y **Eliminar**.</span><span class="sxs-lookup"><span data-stu-id="d1568-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="d1568-140">Si se produce un error en la solicitud de la API web, dicho error se registra en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d1568-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="d1568-141">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="d1568-141">Add a to-do item</span></span>

<span data-ttu-id="d1568-142">En el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d1568-142">In the following code:</span></span>

* <span data-ttu-id="d1568-143">Se declara una variable `item` para construir una representación literal de objeto de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="d1568-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="d1568-144">Una solicitud Fetch se configura con las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="d1568-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="d1568-145">`method`&mdash;especifica el verbo de acción HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="d1568-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="d1568-146">`body`&mdash;especifica la representación JSON del cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d1568-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="d1568-147">El JSON se genera pasando el literal de objeto almacenado en `item` a la función [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="d1568-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="d1568-148">`headers`&mdash;especifica los encabezados de solicitud HTTP `Accept` y `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="d1568-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="d1568-149">Ambos encabezados se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="d1568-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="d1568-150">Se envía una solicitud HTTP POST a la ruta *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="d1568-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="d1568-151">Cuando la API web devuelve un código de estado correcto, se invoca la función `getItems` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="d1568-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="d1568-152">Si se produce un error en la solicitud de la API web, dicho error se registra en la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d1568-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="d1568-153">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="d1568-153">Update a to-do item</span></span>

<span data-ttu-id="d1568-154">Actualizar una tarea pendientes es similar a agregar una; sin embargo, hay dos diferencias importantes:</span><span class="sxs-lookup"><span data-stu-id="d1568-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="d1568-155">La ruta tiene como sufijo el identificador único del elemento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="d1568-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="d1568-156">Por ejemplo, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="d1568-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="d1568-157">El verbo de acción HTTP es PUT, como se indica mediante la opción `method`.</span><span class="sxs-lookup"><span data-stu-id="d1568-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="d1568-158">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="d1568-158">Delete a to-do item</span></span>

<span data-ttu-id="d1568-159">Para eliminar una tarea pendiente, establezca la opción `method` de la solicitud en `DELETE` y especifique el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="d1568-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="d1568-160">Pase al siguiente tutorial para obtener información sobre cómo generar páginas de ayuda de API web:</span><span class="sxs-lookup"><span data-stu-id="d1568-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
