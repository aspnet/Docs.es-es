---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175057"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="49843-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49843-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="49843-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="49843-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="49843-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49843-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="49843-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="49843-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49843-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="49843-107">Create a web API project.</span></span>
> * <span data-ttu-id="49843-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49843-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="49843-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="49843-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="49843-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="49843-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-111">Call the web API with Postman.</span></span>

<span data-ttu-id="49843-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="49843-113">Información general</span><span class="sxs-lookup"><span data-stu-id="49843-113">Overview</span></span>

<span data-ttu-id="49843-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="49843-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49843-115">API</span><span class="sxs-lookup"><span data-stu-id="49843-115">API</span></span> | <span data-ttu-id="49843-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="49843-116">Description</span></span> | <span data-ttu-id="49843-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="49843-117">Request body</span></span> | <span data-ttu-id="49843-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="49843-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="49843-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-119">Get all to-do items</span></span> | <span data-ttu-id="49843-120">Ninguna</span><span class="sxs-lookup"><span data-stu-id="49843-120">None</span></span> | <span data-ttu-id="49843-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="49843-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="49843-122">Get an item by ID</span></span> | <span data-ttu-id="49843-123">None</span><span class="sxs-lookup"><span data-stu-id="49843-123">None</span></span> | <span data-ttu-id="49843-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="49843-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="49843-125">Add a new item</span></span> | <span data-ttu-id="49843-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-126">To-do item</span></span> | <span data-ttu-id="49843-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="49843-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49843-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-129">To-do item</span></span> | <span data-ttu-id="49843-130">None</span><span class="sxs-lookup"><span data-stu-id="49843-130">None</span></span> |
|<span data-ttu-id="49843-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-133">None</span><span class="sxs-lookup"><span data-stu-id="49843-133">None</span></span> | <span data-ttu-id="49843-134">None</span><span class="sxs-lookup"><span data-stu-id="49843-134">None</span></span>|

<span data-ttu-id="49843-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="49843-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="49843-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="49843-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="49843-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="49843-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49843-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="49843-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49843-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 5.0** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="49843-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="49843-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="49843-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49843-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49843-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="49843-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="49843-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="49843-158">The preceding commands:</span></span>

  * <span data-ttu-id="49843-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="49843-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="49843-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="49843-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="49843-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49843-164">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49843-165">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="49843-167">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="49843-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="49843-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-168">Select **Next**.</span></span>

* <span data-ttu-id="49843-169">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="49843-171">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="49843-172">Prueba del proyecto</span><span class="sxs-lookup"><span data-stu-id="49843-172">Test the project</span></span>

<span data-ttu-id="49843-173">La plantilla de proyecto crea una API `WeatherForecast` compatible con [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="49843-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49843-175">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="49843-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="49843-176">Visual Studio inicia lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-176">Visual Studio launches:</span></span>

* <span data-ttu-id="49843-177">El servidor web de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="49843-177">The IIS Express web server.</span></span>
* <span data-ttu-id="49843-178">El explorador predeterminado, y navega hasta `https://localhost:<port>/swagger/index.html`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="49843-180">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49843-181">En un explorador, vaya a la siguiente dirección URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="49843-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49843-183">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49843-184">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49843-185">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="49843-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49843-186">Anexe `/swagger` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="49843-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="49843-187">Se abre la página de Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="49843-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="49843-188">Seleccione **GET** > **Try it out** > **Execute** (GET > Probar > Ejecutar).</span><span class="sxs-lookup"><span data-stu-id="49843-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="49843-189">La página muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-189">The page displays:</span></span>

* <span data-ttu-id="49843-190">Comando de [Curl](https://curl.haxx.se/) para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="49843-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="49843-191">Dirección URL para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="49843-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="49843-192">Código de respuesta, cuerpo y encabezados</span><span class="sxs-lookup"><span data-stu-id="49843-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="49843-193">Cuadro de lista desplegable con los tipos de medios y el esquema y valor de ejemplo</span><span class="sxs-lookup"><span data-stu-id="49843-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="49843-194">Swagger se usa para generar una documentación útil y páginas de ayuda para API web.</span><span class="sxs-lookup"><span data-stu-id="49843-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="49843-195">Este tutorial se centra en cómo crear una API web.</span><span class="sxs-lookup"><span data-stu-id="49843-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="49843-196">Para obtener más información sobre Swagger, vea <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="49843-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="49843-197">Copie y pegue la **URL de solicitud** en el explorador: `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="49843-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="49843-198">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="49843-199">Actualización de launchUrl</span><span class="sxs-lookup"><span data-stu-id="49843-199">Update the launchUrl</span></span>

<span data-ttu-id="49843-200">En *Properties\launchSettings.json*, actualice `launchUrl` de `"swagger"` a `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="49843-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="49843-201">Dado que Swagger se ha quitado, el marcado anterior cambia la dirección URL que se inicia con el método GET del controlador agregado en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="49843-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="49843-202">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="49843-202">Add a model class</span></span>

<span data-ttu-id="49843-203">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="49843-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49843-204">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="49843-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-206">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49843-207">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-208">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="49843-209">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-210">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49843-211">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-213">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49843-214">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-216">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-216">Right-click the project.</span></span> <span data-ttu-id="49843-217">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-218">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-218">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49843-220">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="49843-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49843-221">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="49843-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49843-222">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="49843-223">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="49843-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49843-224">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="49843-225">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="49843-225">Add a database context</span></span>

<span data-ttu-id="49843-226">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49843-227">Esta clase se crea derivándola de la clase <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="49843-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="49843-229">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="49843-229">Add NuGet packages</span></span>

* <span data-ttu-id="49843-230">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="49843-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="49843-231">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="49843-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="49843-232">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="49843-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="49843-233">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="49843-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="49843-234">Use las instrucciones anteriores para agregar el paquete NuGet **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="49843-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="49843-235">![Administrador de paquetes NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="49843-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="49843-236">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="49843-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="49843-237">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-238">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-239">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49843-240">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49843-241">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="49843-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="49843-242">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="49843-242">Register the database context</span></span>

<span data-ttu-id="49843-243">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49843-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49843-244">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="49843-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="49843-245">Actualice *Startup.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="49843-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="49843-246">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="49843-246">The preceding code:</span></span>

* <span data-ttu-id="49843-247">Quita las llamadas de Swagger.</span><span class="sxs-lookup"><span data-stu-id="49843-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="49843-248">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="49843-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49843-249">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="49843-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49843-250">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="49843-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="49843-251">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="49843-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-253">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="49843-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49843-254">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="49843-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49843-255">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="49843-256">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="49843-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="49843-257">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="49843-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="49843-258">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="49843-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="49843-259">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-260">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="49843-261">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="49843-262">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="49843-262">The preceding commands:</span></span>

* <span data-ttu-id="49843-263">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="49843-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="49843-264">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="49843-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="49843-265">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="49843-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="49843-266">El código generado:</span><span class="sxs-lookup"><span data-stu-id="49843-266">The generated code:</span></span>

* <span data-ttu-id="49843-267">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49843-268">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49843-269">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="49843-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49843-270">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49843-271">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="49843-272">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="49843-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="49843-273">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="49843-274">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="49843-275">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="49843-276">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="49843-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="49843-277">Actualización del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="49843-278">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="49843-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="49843-279">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49843-280">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49843-281">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="49843-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49843-282">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="49843-283">Devuelve un [código de estado HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="49843-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="49843-284">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="49843-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49843-285">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="49843-286">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="49843-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="49843-287">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="49843-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49843-288">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="49843-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49843-289">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="49843-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="49843-290">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="49843-290">Install Postman</span></span>

<span data-ttu-id="49843-291">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49843-292">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="49843-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="49843-293">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="49843-293">Start the web app.</span></span>
* <span data-ttu-id="49843-294">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-294">Start Postman.</span></span>
* <span data-ttu-id="49843-295">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="49843-296">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="49843-297">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="49843-298">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="49843-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="49843-299">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-299">Create a new request.</span></span>
* <span data-ttu-id="49843-300">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="49843-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49843-301">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49843-302">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49843-303">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="49843-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="49843-304">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="49843-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49843-305">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="49843-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49843-306">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49843-307">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-307">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="49843-309">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="49843-309">Test the location header URI</span></span>

<span data-ttu-id="49843-310">El URI del encabezado de ubicación se puede probar en el explorador.</span><span class="sxs-lookup"><span data-stu-id="49843-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="49843-311">Copie y pegue el URI del encabezado de ubicación en el explorador.</span><span class="sxs-lookup"><span data-stu-id="49843-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="49843-312">Para probarlo en Postman:</span><span class="sxs-lookup"><span data-stu-id="49843-312">To test in Postman:</span></span>

* <span data-ttu-id="49843-313">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="49843-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49843-314">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="49843-314">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="49843-316">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="49843-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="49843-317">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="49843-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="49843-318">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="49843-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="49843-319">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="49843-320">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="49843-320">Examine the GET methods</span></span>

<span data-ttu-id="49843-321">Se implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="49843-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="49843-322">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="49843-323">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="49843-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="49843-324">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="49843-325">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="49843-325">Test Get with Postman</span></span>

* <span data-ttu-id="49843-326">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-326">Create a new request.</span></span>
* <span data-ttu-id="49843-327">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="49843-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="49843-328">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49843-329">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49843-330">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49843-331">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-331">Select **Send**.</span></span>

<span data-ttu-id="49843-332">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="49843-332">This app uses an in-memory database.</span></span> <span data-ttu-id="49843-333">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="49843-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="49843-334">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="49843-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="49843-335">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="49843-335">Routing and URL paths</span></span>

<span data-ttu-id="49843-336">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="49843-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49843-337">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="49843-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49843-338">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="49843-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="49843-339">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="49843-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49843-340">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="49843-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="49843-341">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="49843-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49843-342">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="49843-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49843-343">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="49843-343">This sample doesn't use a template.</span></span> <span data-ttu-id="49843-344">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="49843-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49843-345">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="49843-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49843-346">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="49843-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="49843-347">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="49843-347">Return values</span></span>

<span data-ttu-id="49843-348">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="49843-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49843-349">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49843-350">El código de respuesta de este tipo de valor devuelto es [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="49843-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49843-351">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="49843-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49843-352">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49843-353">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="49843-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49843-354">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error de [estado 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="49843-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49843-355">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="49843-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49843-356">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="49843-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="49843-357">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-357">The PutTodoItem method</span></span>

<span data-ttu-id="49843-358">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="49843-359">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49843-360">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="49843-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49843-361">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="49843-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49843-362">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49843-363">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="49843-364">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="49843-365">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49843-366">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49843-367">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49843-368">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="49843-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49843-369">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="49843-369">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="49843-371">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="49843-372">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="49843-373">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="49843-374">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49843-375">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="49843-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49843-376">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="49843-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="49843-377">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="49843-378">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="49843-378">Prevent over-posting</span></span>

<span data-ttu-id="49843-379">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="49843-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="49843-380">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="49843-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="49843-381">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="49843-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="49843-382">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="49843-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="49843-383">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="49843-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="49843-384">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="49843-384">A DTO may be used to:</span></span>

* <span data-ttu-id="49843-385">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="49843-385">Prevent over-posting.</span></span>
* <span data-ttu-id="49843-386">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="49843-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="49843-387">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="49843-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="49843-388">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="49843-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="49843-389">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="49843-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="49843-390">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="49843-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="49843-391">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="49843-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="49843-392">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="49843-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="49843-393">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="49843-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="49843-394">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="49843-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="49843-395">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="49843-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="49843-396">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="49843-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="49843-397">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="49843-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="49843-398">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="49843-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49843-399">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="49843-399">Create a web API project.</span></span>
> * <span data-ttu-id="49843-400">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49843-401">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="49843-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="49843-402">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="49843-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="49843-403">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-403">Call the web API with Postman.</span></span>

<span data-ttu-id="49843-404">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="49843-405">Información general</span><span class="sxs-lookup"><span data-stu-id="49843-405">Overview</span></span>

<span data-ttu-id="49843-406">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="49843-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49843-407">API</span><span class="sxs-lookup"><span data-stu-id="49843-407">API</span></span> | <span data-ttu-id="49843-408">Descripción</span><span class="sxs-lookup"><span data-stu-id="49843-408">Description</span></span> | <span data-ttu-id="49843-409">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="49843-409">Request body</span></span> | <span data-ttu-id="49843-410">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="49843-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="49843-411">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-411">Get all to-do items</span></span> | <span data-ttu-id="49843-412">None</span><span class="sxs-lookup"><span data-stu-id="49843-412">None</span></span> | <span data-ttu-id="49843-413">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="49843-414">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="49843-414">Get an item by ID</span></span> | <span data-ttu-id="49843-415">None</span><span class="sxs-lookup"><span data-stu-id="49843-415">None</span></span> | <span data-ttu-id="49843-416">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="49843-417">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="49843-417">Add a new item</span></span> | <span data-ttu-id="49843-418">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-418">To-do item</span></span> | <span data-ttu-id="49843-419">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="49843-420">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49843-421">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-421">To-do item</span></span> | <span data-ttu-id="49843-422">None</span><span class="sxs-lookup"><span data-stu-id="49843-422">None</span></span> |
|<span data-ttu-id="49843-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-424">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-425">None</span><span class="sxs-lookup"><span data-stu-id="49843-425">None</span></span> | <span data-ttu-id="49843-426">None</span><span class="sxs-lookup"><span data-stu-id="49843-426">None</span></span>|

<span data-ttu-id="49843-427">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-427">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="49843-433">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="49843-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="49843-437">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="49843-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-439">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="49843-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49843-440">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="49843-441">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49843-442">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="49843-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="49843-443">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-443">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-446">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="49843-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49843-447">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49843-448">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="49843-449">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="49843-450">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="49843-450">The preceding commands:</span></span>

  * <span data-ttu-id="49843-451">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="49843-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="49843-452">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="49843-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-453">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-454">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="49843-454">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49843-456">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49843-457">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="49843-459">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="49843-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="49843-460">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-460">Select **Next**.</span></span>

* <span data-ttu-id="49843-461">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="49843-463">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="49843-464">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="49843-464">Test the API</span></span>

<span data-ttu-id="49843-465">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="49843-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="49843-466">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49843-468">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49843-469">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="49843-470">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="49843-471">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49843-473">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49843-474">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="49843-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-475">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49843-476">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49843-477">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49843-478">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="49843-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49843-479">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="49843-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="49843-480">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-480">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="49843-481">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="49843-481">Add a model class</span></span>

<span data-ttu-id="49843-482">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="49843-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49843-483">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="49843-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-485">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49843-486">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-487">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="49843-488">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-489">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49843-490">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-492">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49843-493">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-494">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-495">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-495">Right-click the project.</span></span> <span data-ttu-id="49843-496">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-497">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-497">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49843-499">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="49843-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49843-500">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="49843-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49843-501">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="49843-502">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="49843-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49843-503">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="49843-504">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="49843-504">Add a database context</span></span>

<span data-ttu-id="49843-505">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49843-506">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="49843-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="49843-508">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="49843-508">Add NuGet packages</span></span>

* <span data-ttu-id="49843-509">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="49843-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="49843-510">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="49843-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="49843-511">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="49843-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="49843-512">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="49843-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="49843-513">Use las instrucciones anteriores para agregar el paquete NuGet **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="49843-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="49843-515">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="49843-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="49843-516">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-517">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-518">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49843-519">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49843-520">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="49843-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="49843-521">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="49843-521">Register the database context</span></span>

<span data-ttu-id="49843-522">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49843-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49843-523">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="49843-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="49843-524">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="49843-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="49843-525">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="49843-525">The preceding code:</span></span>

* <span data-ttu-id="49843-526">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="49843-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49843-527">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="49843-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49843-528">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="49843-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="49843-529">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="49843-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-531">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="49843-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49843-532">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="49843-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="49843-533">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="49843-534">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="49843-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="49843-535">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="49843-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="49843-536">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="49843-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="49843-537">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-538">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="49843-539">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="49843-540">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="49843-540">The preceding commands:</span></span>

* <span data-ttu-id="49843-541">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="49843-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="49843-542">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="49843-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="49843-543">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="49843-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="49843-544">El código generado:</span><span class="sxs-lookup"><span data-stu-id="49843-544">The generated code:</span></span>

* <span data-ttu-id="49843-545">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49843-546">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49843-547">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="49843-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49843-548">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49843-549">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="49843-550">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="49843-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="49843-551">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="49843-552">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="49843-553">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="49843-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="49843-554">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="49843-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="49843-555">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="49843-556">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="49843-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="49843-557">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49843-558">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49843-559">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="49843-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49843-560">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="49843-561">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="49843-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="49843-562">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="49843-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49843-563">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="49843-564">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="49843-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="49843-565">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="49843-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49843-566">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="49843-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49843-567">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="49843-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="49843-568">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="49843-568">Install Postman</span></span>

<span data-ttu-id="49843-569">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49843-570">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="49843-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="49843-571">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="49843-571">Start the web app.</span></span>
* <span data-ttu-id="49843-572">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-572">Start Postman.</span></span>
* <span data-ttu-id="49843-573">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="49843-574">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="49843-575">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="49843-576">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="49843-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="49843-577">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-577">Create a new request.</span></span>
* <span data-ttu-id="49843-578">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="49843-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49843-579">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49843-580">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49843-581">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="49843-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="49843-582">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="49843-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49843-583">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="49843-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49843-584">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49843-585">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-585">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="49843-587">Prueba del URI del encabezado de ubicación con Postman</span><span class="sxs-lookup"><span data-stu-id="49843-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="49843-588">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="49843-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49843-589">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="49843-589">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="49843-591">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="49843-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="49843-592">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="49843-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="49843-593">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="49843-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="49843-594">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="49843-595">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="49843-595">Examine the GET methods</span></span>

<span data-ttu-id="49843-596">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="49843-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="49843-597">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="49843-598">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="49843-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="49843-599">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="49843-600">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="49843-600">Test Get with Postman</span></span>

* <span data-ttu-id="49843-601">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-601">Create a new request.</span></span>
* <span data-ttu-id="49843-602">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="49843-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="49843-603">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="49843-604">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="49843-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="49843-605">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49843-606">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-606">Select **Send**.</span></span>

<span data-ttu-id="49843-607">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="49843-607">This app uses an in-memory database.</span></span> <span data-ttu-id="49843-608">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="49843-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="49843-609">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="49843-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="49843-610">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="49843-610">Routing and URL paths</span></span>

<span data-ttu-id="49843-611">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="49843-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49843-612">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="49843-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49843-613">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="49843-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="49843-614">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="49843-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49843-615">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="49843-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="49843-616">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="49843-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49843-617">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="49843-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49843-618">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="49843-618">This sample doesn't use a template.</span></span> <span data-ttu-id="49843-619">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="49843-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49843-620">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="49843-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49843-621">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="49843-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="49843-622">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="49843-622">Return values</span></span> 

<span data-ttu-id="49843-623">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="49843-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49843-624">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49843-625">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="49843-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49843-626">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="49843-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49843-627">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49843-628">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="49843-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49843-629">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="49843-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49843-630">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="49843-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49843-631">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="49843-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="49843-632">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-632">The PutTodoItem method</span></span>

<span data-ttu-id="49843-633">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="49843-634">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49843-635">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="49843-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49843-636">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="49843-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49843-637">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49843-638">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="49843-639">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="49843-640">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49843-641">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49843-642">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49843-643">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="49843-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49843-644">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="49843-644">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="49843-646">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="49843-647">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="49843-648">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="49843-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="49843-649">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49843-650">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="49843-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49843-651">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="49843-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="49843-652">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="49843-653">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="49843-653">Prevent over-posting</span></span>

<span data-ttu-id="49843-654">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="49843-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="49843-655">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="49843-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="49843-656">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="49843-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="49843-657">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="49843-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="49843-658">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="49843-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="49843-659">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="49843-659">A DTO may be used to:</span></span>

* <span data-ttu-id="49843-660">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="49843-660">Prevent over-posting.</span></span>
* <span data-ttu-id="49843-661">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="49843-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="49843-662">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="49843-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="49843-663">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="49843-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="49843-664">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="49843-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="49843-665">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="49843-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="49843-666">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="49843-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="49843-667">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="49843-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="49843-668">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="49843-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="49843-669">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="49843-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="49843-670">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="49843-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="49843-671">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="49843-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="49843-672">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="49843-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49843-673">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="49843-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49843-674">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="49843-674">Create a web API project.</span></span>
> * <span data-ttu-id="49843-675">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="49843-676">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-676">Add a controller.</span></span>
> * <span data-ttu-id="49843-677">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="49843-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="49843-678">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="49843-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="49843-679">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="49843-679">Specify return values.</span></span>
> * <span data-ttu-id="49843-680">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="49843-681">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49843-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="49843-682">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="49843-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="49843-683">Información general (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-683">Overview 2.1</span></span>

<span data-ttu-id="49843-684">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="49843-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="49843-685">API</span><span class="sxs-lookup"><span data-stu-id="49843-685">API</span></span> | <span data-ttu-id="49843-686">Descripción</span><span class="sxs-lookup"><span data-stu-id="49843-686">Description</span></span> | <span data-ttu-id="49843-687">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="49843-687">Request body</span></span> | <span data-ttu-id="49843-688">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="49843-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="49843-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="49843-689">GET /api/TodoItems</span></span> | <span data-ttu-id="49843-690">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-690">Get all to-do items</span></span> | <span data-ttu-id="49843-691">None</span><span class="sxs-lookup"><span data-stu-id="49843-691">None</span></span> | <span data-ttu-id="49843-692">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="49843-692">Array of to-do items</span></span>|
|<span data-ttu-id="49843-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="49843-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="49843-694">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="49843-694">Get an item by ID</span></span> | <span data-ttu-id="49843-695">None</span><span class="sxs-lookup"><span data-stu-id="49843-695">None</span></span> | <span data-ttu-id="49843-696">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-696">To-do item</span></span>|
|<span data-ttu-id="49843-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="49843-697">POST /api/TodoItems</span></span> | <span data-ttu-id="49843-698">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="49843-698">Add a new item</span></span> | <span data-ttu-id="49843-699">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-699">To-do item</span></span> | <span data-ttu-id="49843-700">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-700">To-do item</span></span> |
|<span data-ttu-id="49843-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="49843-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="49843-702">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="49843-703">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="49843-703">To-do item</span></span> | <span data-ttu-id="49843-704">None</span><span class="sxs-lookup"><span data-stu-id="49843-704">None</span></span> |
|<span data-ttu-id="49843-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-706">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="49843-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="49843-707">None</span><span class="sxs-lookup"><span data-stu-id="49843-707">None</span></span> | <span data-ttu-id="49843-708">None</span><span class="sxs-lookup"><span data-stu-id="49843-708">None</span></span>|

<span data-ttu-id="49843-709">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-709">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="49843-715">Requisitos previos (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-718">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="49843-719">Creación de un proyecto web (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-721">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="49843-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49843-722">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="49843-723">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="49843-724">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="49843-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="49843-725">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="49843-726">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="49843-726">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-729">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="49843-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="49843-730">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="49843-731">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="49843-732">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="49843-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="49843-733">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-734">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-735">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="49843-735">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="49843-737">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="49843-738">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="49843-739">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="49843-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="49843-740">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="49843-740">Select **Next**.</span></span>

* <span data-ttu-id="49843-741">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="49843-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="49843-743">Prueba de la API (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-743">Test the API 2.1</span></span>

<span data-ttu-id="49843-744">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="49843-744">The project template creates a `values` API.</span></span> <span data-ttu-id="49843-745">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49843-747">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49843-748">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="49843-749">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="49843-750">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="49843-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49843-752">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="49843-753">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="49843-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-754">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="49843-755">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="49843-756">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="49843-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="49843-757">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="49843-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="49843-758">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="49843-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="49843-759">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="49843-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="49843-760">Incorporación de una clase de modelo (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-760">Add a model class 2.1</span></span>

<span data-ttu-id="49843-761">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="49843-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="49843-762">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="49843-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-764">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="49843-765">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-766">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="49843-767">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-768">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="49843-769">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49843-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49843-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49843-771">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="49843-772">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49843-773">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49843-774">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-774">Right-click the project.</span></span> <span data-ttu-id="49843-775">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="49843-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="49843-776">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-776">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="49843-778">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="49843-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="49843-779">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="49843-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="49843-780">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="49843-781">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="49843-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="49843-782">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="49843-783">Incorporación de un contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-783">Add a database context 2.1</span></span>

<span data-ttu-id="49843-784">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="49843-785">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="49843-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-787">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="49843-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="49843-788">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-789">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49843-790">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="49843-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="49843-791">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="49843-792">Registro del contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-792">Register the database context 2.1</span></span>

<span data-ttu-id="49843-793">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49843-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="49843-794">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="49843-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="49843-795">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="49843-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="49843-796">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="49843-796">The preceding code:</span></span>

* <span data-ttu-id="49843-797">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="49843-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="49843-798">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="49843-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="49843-799">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="49843-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="49843-800">Incorporación de un controlador (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-802">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="49843-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="49843-803">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="49843-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="49843-804">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="49843-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="49843-805">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49843-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-807">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49843-808">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="49843-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="49843-809">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="49843-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="49843-810">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="49843-810">The preceding code:</span></span>

* <span data-ttu-id="49843-811">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="49843-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="49843-812">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="49843-813">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="49843-814">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="49843-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="49843-815">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="49843-816">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="49843-817">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="49843-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="49843-818">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="49843-819">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="49843-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="49843-820">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="49843-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="49843-821">Incorporación de métodos Get (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-821">Add Get methods 2.1</span></span>

<span data-ttu-id="49843-822">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="49843-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="49843-823">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="49843-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="49843-824">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="49843-824">Stop the app if it's still running.</span></span> <span data-ttu-id="49843-825">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="49843-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="49843-826">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="49843-827">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="49843-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="49843-828">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="49843-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="49843-829">Enrutamiento y rutas URL (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="49843-830">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="49843-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="49843-831">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="49843-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="49843-832">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="49843-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="49843-833">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="49843-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="49843-834">En este ejemplo, el nombre de clase de controlador es **Todo** Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="49843-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="49843-835">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="49843-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="49843-836">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="49843-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="49843-837">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="49843-837">This sample doesn't use a template.</span></span> <span data-ttu-id="49843-838">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="49843-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="49843-839">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="49843-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="49843-840">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="49843-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="49843-841">Valores devueltos (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-841">Return values 2.1</span></span>

<span data-ttu-id="49843-842">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="49843-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="49843-843">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="49843-844">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="49843-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="49843-845">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="49843-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="49843-846">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="49843-847">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="49843-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="49843-848">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="49843-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="49843-849">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="49843-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="49843-850">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="49843-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="49843-851">Prueba del método GetTodoItems (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="49843-852">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="49843-853">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="49843-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="49843-854">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="49843-854">Start the web app.</span></span>
* <span data-ttu-id="49843-855">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-855">Start Postman.</span></span>
* <span data-ttu-id="49843-856">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49843-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49843-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49843-858">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="49843-859">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49843-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="49843-860">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="49843-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="49843-861">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="49843-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="49843-862">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="49843-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="49843-863">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-863">Create a new request.</span></span>
  * <span data-ttu-id="49843-864">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="49843-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="49843-865">Establezca el URI de solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="49843-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="49843-866">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="49843-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="49843-867">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="49843-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="49843-868">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-868">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="49843-870">Incorporación de un método Create (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-870">Add a Create method 2.1</span></span>

<span data-ttu-id="49843-871">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="49843-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="49843-872">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="49843-873">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="49843-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="49843-874">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="49843-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="49843-875">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="49843-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="49843-876">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="49843-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="49843-877">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="49843-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="49843-878">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="49843-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="49843-879">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="49843-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="49843-880">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="49843-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="49843-881">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="49843-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="49843-882">Prueba del método PostTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="49843-883">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-883">Build the project.</span></span>
* <span data-ttu-id="49843-884">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="49843-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="49843-885">Establezca el URI en `https://localhost:<port>/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="49843-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="49843-886">Por ejemplo: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="49843-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="49843-887">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="49843-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="49843-888">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="49843-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="49843-889">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="49843-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="49843-890">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="49843-891">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-891">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="49843-893">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="49843-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="49843-894">Prueba del URI del encabezado de ubicación (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="49843-895">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="49843-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="49843-896">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="49843-896">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="49843-898">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="49843-898">Set the method to GET.</span></span>
* <span data-ttu-id="49843-899">Establezca el URI en `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="49843-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="49843-900">Por ejemplo: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="49843-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="49843-901">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="49843-902">Incorporación de un método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="49843-903">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="49843-904">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="49843-905">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="49843-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="49843-906">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="49843-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="49843-907">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="49843-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="49843-908">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="49843-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="49843-909">Prueba del método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="49843-910">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49843-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="49843-911">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="49843-912">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="49843-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="49843-913">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="49843-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="49843-914">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="49843-914">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="49843-916">Incorporación de un método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="49843-917">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="49843-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="49843-918">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="49843-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="49843-919">Prueba del método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="49843-920">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="49843-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="49843-921">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="49843-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="49843-922">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="49843-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="49843-923">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="49843-923">Select **Send**.</span></span>

<span data-ttu-id="49843-924">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="49843-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="49843-925">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="49843-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="49843-926">Llamada a la API web con JavaScript (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="49843-927">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="49843-928">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-928">jQuery initiates the request.</span></span> <span data-ttu-id="49843-929">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="49843-930">Configure la aplicación para [atender archivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) y [habilitar la asignación de archivos predeterminada](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="49843-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="49843-931">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="49843-932">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="49843-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="49843-933">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="49843-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="49843-934">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="49843-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="49843-935">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="49843-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="49843-936">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="49843-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="49843-937">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="49843-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="49843-938">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="49843-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="49843-939">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="49843-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="49843-940">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="49843-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="49843-941">Obtención de una lista de tareas pendientes (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="49843-942">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="49843-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="49843-943">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="49843-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="49843-944">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="49843-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="49843-945">Incorporación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="49843-946">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="49843-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="49843-947">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="49843-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="49843-948">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="49843-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="49843-949">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="49843-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="49843-950">Actualización de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="49843-951">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="49843-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="49843-952">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="49843-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="49843-953">Eliminación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="49843-954">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="49843-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="49843-955">Incorporación de compatibilidad de autenticación a una API web (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="49843-956">Recursos adicionales (2.1)</span><span class="sxs-lookup"><span data-stu-id="49843-956">Additional resources 2.1</span></span>

<span data-ttu-id="49843-957">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="49843-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="49843-958">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="49843-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="49843-959">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="49843-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="49843-960">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="49843-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
