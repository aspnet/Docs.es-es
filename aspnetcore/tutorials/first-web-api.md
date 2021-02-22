---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564078"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0a860-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a860-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0a860-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0a860-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0a860-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a860-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0a860-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="0a860-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a860-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-107">Create a web API project.</span></span>
> * <span data-ttu-id="0a860-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a860-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="0a860-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0a860-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="0a860-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0a860-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0a860-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a860-113">Información general</span><span class="sxs-lookup"><span data-stu-id="0a860-113">Overview</span></span>

<span data-ttu-id="0a860-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="0a860-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a860-115">API</span><span class="sxs-lookup"><span data-stu-id="0a860-115">API</span></span> | <span data-ttu-id="0a860-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a860-116">Description</span></span> | <span data-ttu-id="0a860-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="0a860-117">Request body</span></span> | <span data-ttu-id="0a860-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="0a860-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0a860-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-119">Get all to-do items</span></span> | <span data-ttu-id="0a860-120">None</span><span class="sxs-lookup"><span data-stu-id="0a860-120">None</span></span> | <span data-ttu-id="0a860-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0a860-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="0a860-122">Get an item by ID</span></span> | <span data-ttu-id="0a860-123">None</span><span class="sxs-lookup"><span data-stu-id="0a860-123">None</span></span> | <span data-ttu-id="0a860-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0a860-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="0a860-125">Add a new item</span></span> | <span data-ttu-id="0a860-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-126">To-do item</span></span> | <span data-ttu-id="0a860-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0a860-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a860-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-129">To-do item</span></span> | <span data-ttu-id="0a860-130">None</span><span class="sxs-lookup"><span data-stu-id="0a860-130">None</span></span> |
|<span data-ttu-id="0a860-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-133">None</span><span class="sxs-lookup"><span data-stu-id="0a860-133">None</span></span> | <span data-ttu-id="0a860-134">None</span><span class="sxs-lookup"><span data-stu-id="0a860-134">None</span></span>|

<span data-ttu-id="0a860-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a860-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="0a860-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a860-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="0a860-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0a860-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a860-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a860-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a860-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 5.0** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="0a860-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0a860-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a860-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a860-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0a860-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0a860-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0a860-158">The preceding commands:</span></span>

  * <span data-ttu-id="0a860-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0a860-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0a860-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="0a860-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a860-164">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a860-165">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0a860-167">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="0a860-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="0a860-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-168">Select **Next**.</span></span>

* <span data-ttu-id="0a860-169">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0a860-171">Abra un terminal de comandos en la carpeta del proyecto y ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="0a860-172">Prueba del proyecto</span><span class="sxs-lookup"><span data-stu-id="0a860-172">Test the project</span></span>

<span data-ttu-id="0a860-173">La plantilla de proyecto crea una API `WeatherForecast` compatible con [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="0a860-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a860-175">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="0a860-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0a860-176">Visual Studio inicia lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-176">Visual Studio launches:</span></span>

* <span data-ttu-id="0a860-177">El servidor web de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0a860-177">The IIS Express web server.</span></span>
* <span data-ttu-id="0a860-178">El explorador predeterminado, y navega hasta `https://localhost:<port>/swagger/index.html`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="0a860-180">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a860-181">En un explorador, vaya a la siguiente dirección URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="0a860-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a860-183">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a860-184">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a860-185">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="0a860-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a860-186">Anexe `/swagger` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="0a860-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="0a860-187">Se abre la página de Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="0a860-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="0a860-188">Seleccione **GET** > **Try it out** > **Execute** (GET > Probar > Ejecutar).</span><span class="sxs-lookup"><span data-stu-id="0a860-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="0a860-189">La página muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-189">The page displays:</span></span>

* <span data-ttu-id="0a860-190">Comando de [Curl](https://curl.haxx.se/) para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="0a860-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0a860-191">Dirección URL para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="0a860-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0a860-192">Código de respuesta, cuerpo y encabezados</span><span class="sxs-lookup"><span data-stu-id="0a860-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="0a860-193">Cuadro de lista desplegable con los tipos de medios y el esquema y valor de ejemplo</span><span class="sxs-lookup"><span data-stu-id="0a860-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="0a860-194">Swagger se usa para generar una documentación útil y páginas de ayuda para API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="0a860-195">Este tutorial se centra en cómo crear una API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="0a860-196">Para obtener más información sobre Swagger, vea <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="0a860-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="0a860-197">Copie y pegue la **URL de solicitud** en el explorador: `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0a860-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="0a860-198">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="0a860-199">Actualización de launchUrl</span><span class="sxs-lookup"><span data-stu-id="0a860-199">Update the launchUrl</span></span>

<span data-ttu-id="0a860-200">En *Properties\launchSettings.json*, actualice `launchUrl` de `"swagger"` a `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="0a860-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="0a860-201">Dado que Swagger se ha quitado, el marcado anterior cambia la dirección URL que se inicia con el método GET del controlador agregado en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="0a860-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="0a860-202">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="0a860-202">Add a model class</span></span>

<span data-ttu-id="0a860-203">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="0a860-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a860-204">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="0a860-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-206">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0a860-207">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-208">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a860-209">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-210">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a860-211">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-213">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a860-214">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-216">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-216">Right-click the project.</span></span> <span data-ttu-id="0a860-217">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-218">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-218">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a860-220">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="0a860-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a860-221">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0a860-222">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0a860-223">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="0a860-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a860-224">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a860-225">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="0a860-225">Add a database context</span></span>

<span data-ttu-id="0a860-226">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a860-227">Esta clase se crea derivándola de la clase <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0a860-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0a860-229">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="0a860-229">Add NuGet packages</span></span>

* <span data-ttu-id="0a860-230">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="0a860-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0a860-231">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.InMemory** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0a860-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="0a860-232">Seleccione **Microsoft.EntityFrameworkCore.InMemory** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="0a860-232">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="0a860-233">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0a860-235">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="0a860-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="0a860-236">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-237">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-238">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a860-239">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a860-240">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0a860-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a860-241">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="0a860-241">Register the database context</span></span>

<span data-ttu-id="0a860-242">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a860-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a860-243">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="0a860-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a860-244">Actualice *Startup.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0a860-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0a860-245">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="0a860-245">The preceding code:</span></span>

* <span data-ttu-id="0a860-246">Quita las llamadas de Swagger.</span><span class="sxs-lookup"><span data-stu-id="0a860-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="0a860-247">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a860-248">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="0a860-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a860-249">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="0a860-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0a860-250">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="0a860-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-252">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="0a860-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a860-253">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="0a860-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0a860-254">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0a860-255">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="0a860-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0a860-256">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0a860-257">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="0a860-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0a860-258">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-259">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a860-260">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0a860-261">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0a860-261">The preceding commands:</span></span>

* <span data-ttu-id="0a860-262">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0a860-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0a860-263">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="0a860-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0a860-264">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="0a860-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0a860-265">El código generado:</span><span class="sxs-lookup"><span data-stu-id="0a860-265">The generated code:</span></span>

* <span data-ttu-id="0a860-266">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a860-267">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a860-268">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0a860-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a860-269">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a860-270">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0a860-271">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="0a860-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0a860-272">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0a860-273">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0a860-274">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0a860-275">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="0a860-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="0a860-276">Actualización del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="0a860-277">Actualice la instrucción "return" en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="0a860-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0a860-278">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a860-279">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a860-280">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a860-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a860-281">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0a860-282">Devuelve un [código de estado HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="0a860-283">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0a860-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a860-284">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0a860-285">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="0a860-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0a860-286">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a860-287">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="0a860-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a860-288">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0a860-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0a860-289">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-289">Install Postman</span></span>

<span data-ttu-id="0a860-290">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a860-291">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0a860-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0a860-292">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0a860-292">Start the web app.</span></span>
* <span data-ttu-id="0a860-293">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-293">Start Postman.</span></span>
* <span data-ttu-id="0a860-294">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0a860-295">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0a860-296">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0a860-297">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0a860-298">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-298">Create a new request.</span></span>
* <span data-ttu-id="0a860-299">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="0a860-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a860-300">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a860-301">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a860-302">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a860-303">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="0a860-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a860-304">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0a860-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a860-305">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a860-306">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-306">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0a860-308">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="0a860-308">Test the location header URI</span></span>

<span data-ttu-id="0a860-309">El URI del encabezado de ubicación se puede probar en el explorador.</span><span class="sxs-lookup"><span data-stu-id="0a860-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="0a860-310">Copie y pegue el URI del encabezado de ubicación en el explorador.</span><span class="sxs-lookup"><span data-stu-id="0a860-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="0a860-311">Para probarlo en Postman:</span><span class="sxs-lookup"><span data-stu-id="0a860-311">To test in Postman:</span></span>

* <span data-ttu-id="0a860-312">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a860-313">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="0a860-313">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="0a860-315">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="0a860-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0a860-316">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a860-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0a860-317">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a860-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0a860-318">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0a860-319">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="0a860-319">Examine the GET methods</span></span>

<span data-ttu-id="0a860-320">Se implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="0a860-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0a860-321">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0a860-322">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0a860-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0a860-323">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0a860-324">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-324">Test Get with Postman</span></span>

* <span data-ttu-id="0a860-325">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-325">Create a new request.</span></span>
* <span data-ttu-id="0a860-326">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="0a860-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0a860-327">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a860-328">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a860-329">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a860-330">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-330">Select **Send**.</span></span>

<span data-ttu-id="0a860-331">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="0a860-331">This app uses an in-memory database.</span></span> <span data-ttu-id="0a860-332">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="0a860-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0a860-333">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="0a860-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a860-334">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="0a860-334">Routing and URL paths</span></span>

<span data-ttu-id="0a860-335">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="0a860-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a860-336">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="0a860-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a860-337">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="0a860-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0a860-338">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="0a860-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a860-339">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="0a860-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0a860-340">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0a860-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a860-341">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="0a860-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a860-342">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a860-342">This sample doesn't use a template.</span></span> <span data-ttu-id="0a860-343">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a860-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a860-344">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a860-345">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="0a860-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a860-346">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="0a860-346">Return values</span></span>

<span data-ttu-id="0a860-347">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0a860-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a860-348">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a860-349">El código de respuesta de este tipo de valor devuelto es [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="0a860-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a860-350">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="0a860-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a860-351">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a860-352">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="0a860-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a860-353">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error de [estado 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="0a860-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a860-354">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="0a860-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a860-355">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0a860-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0a860-356">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-356">The PutTodoItem method</span></span>

<span data-ttu-id="0a860-357">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0a860-358">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a860-359">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a860-360">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a860-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a860-361">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a860-362">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a860-363">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a860-364">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a860-365">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a860-366">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a860-367">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="0a860-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a860-368">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="0a860-368">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0a860-370">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="0a860-371">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a860-372">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a860-373">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a860-374">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0a860-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a860-375">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="0a860-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a860-376">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0a860-377">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="0a860-377">Prevent over-posting</span></span>

<span data-ttu-id="0a860-378">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0a860-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0a860-379">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="0a860-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0a860-380">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="0a860-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0a860-381">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="0a860-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0a860-382">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="0a860-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="0a860-383">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="0a860-383">A DTO may be used to:</span></span>

* <span data-ttu-id="0a860-384">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-384">Prevent over-posting.</span></span>
* <span data-ttu-id="0a860-385">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="0a860-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0a860-386">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="0a860-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0a860-387">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="0a860-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0a860-388">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="0a860-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0a860-389">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="0a860-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="0a860-390">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="0a860-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0a860-391">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="0a860-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0a860-392">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="0a860-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0a860-393">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="0a860-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0a860-394">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="0a860-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a860-395">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="0a860-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a860-396">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0a860-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0a860-397">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="0a860-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a860-398">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-398">Create a web API project.</span></span>
> * <span data-ttu-id="0a860-399">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a860-400">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="0a860-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0a860-401">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="0a860-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0a860-402">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-402">Call the web API with Postman.</span></span>

<span data-ttu-id="0a860-403">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a860-404">Información general</span><span class="sxs-lookup"><span data-stu-id="0a860-404">Overview</span></span>

<span data-ttu-id="0a860-405">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="0a860-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a860-406">API</span><span class="sxs-lookup"><span data-stu-id="0a860-406">API</span></span> | <span data-ttu-id="0a860-407">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a860-407">Description</span></span> | <span data-ttu-id="0a860-408">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="0a860-408">Request body</span></span> | <span data-ttu-id="0a860-409">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="0a860-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0a860-410">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-410">Get all to-do items</span></span> | <span data-ttu-id="0a860-411">None</span><span class="sxs-lookup"><span data-stu-id="0a860-411">None</span></span> | <span data-ttu-id="0a860-412">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0a860-413">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="0a860-413">Get an item by ID</span></span> | <span data-ttu-id="0a860-414">None</span><span class="sxs-lookup"><span data-stu-id="0a860-414">None</span></span> | <span data-ttu-id="0a860-415">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0a860-416">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="0a860-416">Add a new item</span></span> | <span data-ttu-id="0a860-417">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-417">To-do item</span></span> | <span data-ttu-id="0a860-418">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0a860-419">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a860-420">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-420">To-do item</span></span> | <span data-ttu-id="0a860-421">None</span><span class="sxs-lookup"><span data-stu-id="0a860-421">None</span></span> |
|<span data-ttu-id="0a860-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-423">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-424">None</span><span class="sxs-lookup"><span data-stu-id="0a860-424">None</span></span> | <span data-ttu-id="0a860-425">None</span><span class="sxs-lookup"><span data-stu-id="0a860-425">None</span></span>|

<span data-ttu-id="0a860-426">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-426">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a860-432">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="0a860-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-435">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a860-436">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="0a860-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-438">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0a860-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a860-439">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a860-440">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a860-441">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0a860-442">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-442">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-445">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0a860-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a860-446">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a860-447">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0a860-448">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0a860-449">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0a860-449">The preceding commands:</span></span>

  * <span data-ttu-id="0a860-450">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0a860-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0a860-451">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-452">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-453">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="0a860-453">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a860-455">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a860-456">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0a860-458">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="0a860-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0a860-459">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-459">Select **Next**.</span></span>

* <span data-ttu-id="0a860-460">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0a860-462">Abra un terminal de comandos en la carpeta del proyecto y ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0a860-463">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="0a860-463">Test the API</span></span>

<span data-ttu-id="0a860-464">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0a860-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0a860-465">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a860-467">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a860-468">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a860-469">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a860-470">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a860-472">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a860-473">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0a860-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-474">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a860-475">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a860-476">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a860-477">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="0a860-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a860-478">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="0a860-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0a860-479">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-479">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="0a860-480">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="0a860-480">Add a model class</span></span>

<span data-ttu-id="0a860-481">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="0a860-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a860-482">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="0a860-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-484">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0a860-485">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-486">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a860-487">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-488">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a860-489">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-491">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a860-492">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-493">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-494">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-494">Right-click the project.</span></span> <span data-ttu-id="0a860-495">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-496">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-496">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a860-498">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="0a860-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a860-499">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0a860-500">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0a860-501">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="0a860-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a860-502">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a860-503">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="0a860-503">Add a database context</span></span>

<span data-ttu-id="0a860-504">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a860-505">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0a860-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0a860-507">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="0a860-507">Add NuGet packages</span></span>

* <span data-ttu-id="0a860-508">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="0a860-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0a860-509">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.InMemory** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="0a860-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="0a860-510">Seleccione **Microsoft.EntityFrameworkCore.InMemory** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="0a860-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="0a860-511">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0a860-513">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="0a860-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="0a860-514">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-515">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-516">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a860-517">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a860-518">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0a860-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a860-519">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="0a860-519">Register the database context</span></span>

<span data-ttu-id="0a860-520">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a860-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a860-521">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="0a860-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a860-522">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="0a860-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0a860-523">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="0a860-523">The preceding code:</span></span>

* <span data-ttu-id="0a860-524">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a860-525">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="0a860-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a860-526">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="0a860-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0a860-527">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="0a860-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-529">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="0a860-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a860-530">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="0a860-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0a860-531">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0a860-532">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="0a860-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0a860-533">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0a860-534">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="0a860-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0a860-535">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-536">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a860-537">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0a860-538">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0a860-538">The preceding commands:</span></span>

* <span data-ttu-id="0a860-539">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0a860-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0a860-540">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="0a860-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0a860-541">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="0a860-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0a860-542">El código generado:</span><span class="sxs-lookup"><span data-stu-id="0a860-542">The generated code:</span></span>

* <span data-ttu-id="0a860-543">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a860-544">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a860-545">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0a860-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a860-546">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a860-547">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0a860-548">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="0a860-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0a860-549">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0a860-550">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0a860-551">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="0a860-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0a860-552">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="0a860-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0a860-553">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0a860-554">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="0a860-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0a860-555">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a860-556">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a860-557">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a860-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a860-558">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0a860-559">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0a860-560">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0a860-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a860-561">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0a860-562">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="0a860-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0a860-563">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a860-564">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="0a860-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a860-565">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0a860-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0a860-566">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-566">Install Postman</span></span>

<span data-ttu-id="0a860-567">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a860-568">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0a860-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0a860-569">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0a860-569">Start the web app.</span></span>
* <span data-ttu-id="0a860-570">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-570">Start Postman.</span></span>
* <span data-ttu-id="0a860-571">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0a860-572">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0a860-573">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0a860-574">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0a860-575">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-575">Create a new request.</span></span>
* <span data-ttu-id="0a860-576">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="0a860-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a860-577">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a860-578">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a860-579">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a860-580">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="0a860-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a860-581">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0a860-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a860-582">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a860-583">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-583">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="0a860-585">Prueba del URI del encabezado de ubicación con Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="0a860-586">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a860-587">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="0a860-587">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="0a860-589">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="0a860-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0a860-590">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a860-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0a860-591">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0a860-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0a860-592">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0a860-593">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="0a860-593">Examine the GET methods</span></span>

<span data-ttu-id="0a860-594">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="0a860-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0a860-595">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0a860-596">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0a860-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0a860-597">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0a860-598">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="0a860-598">Test Get with Postman</span></span>

* <span data-ttu-id="0a860-599">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-599">Create a new request.</span></span>
* <span data-ttu-id="0a860-600">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="0a860-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0a860-601">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a860-602">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a860-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a860-603">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a860-604">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-604">Select **Send**.</span></span>

<span data-ttu-id="0a860-605">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="0a860-605">This app uses an in-memory database.</span></span> <span data-ttu-id="0a860-606">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="0a860-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0a860-607">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="0a860-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a860-608">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="0a860-608">Routing and URL paths</span></span>

<span data-ttu-id="0a860-609">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="0a860-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a860-610">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="0a860-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a860-611">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="0a860-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0a860-612">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="0a860-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a860-613">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="0a860-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0a860-614">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0a860-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a860-615">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="0a860-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a860-616">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a860-616">This sample doesn't use a template.</span></span> <span data-ttu-id="0a860-617">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a860-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a860-618">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a860-619">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="0a860-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a860-620">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="0a860-620">Return values</span></span> 

<span data-ttu-id="0a860-621">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0a860-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a860-622">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a860-623">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="0a860-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a860-624">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="0a860-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a860-625">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a860-626">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="0a860-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a860-627">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="0a860-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a860-628">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="0a860-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a860-629">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0a860-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0a860-630">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-630">The PutTodoItem method</span></span>

<span data-ttu-id="0a860-631">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0a860-632">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a860-633">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a860-634">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a860-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a860-635">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a860-636">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a860-637">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a860-638">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a860-639">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a860-640">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a860-641">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="0a860-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a860-642">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="0a860-642">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0a860-644">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="0a860-645">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a860-646">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a860-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a860-647">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a860-648">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0a860-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a860-649">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="0a860-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a860-650">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0a860-651">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="0a860-651">Prevent over-posting</span></span>

<span data-ttu-id="0a860-652">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0a860-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0a860-653">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="0a860-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0a860-654">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="0a860-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0a860-655">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="0a860-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0a860-656">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="0a860-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="0a860-657">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="0a860-657">A DTO may be used to:</span></span>

* <span data-ttu-id="0a860-658">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-658">Prevent over-posting.</span></span>
* <span data-ttu-id="0a860-659">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="0a860-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0a860-660">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="0a860-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0a860-661">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="0a860-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0a860-662">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="0a860-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0a860-663">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="0a860-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0a860-664">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="0a860-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0a860-665">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="0a860-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0a860-666">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="0a860-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0a860-667">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="0a860-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0a860-668">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="0a860-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a860-669">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="0a860-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a860-670">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0a860-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a860-671">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="0a860-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a860-672">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-672">Create a web API project.</span></span>
> * <span data-ttu-id="0a860-673">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a860-674">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-674">Add a controller.</span></span>
> * <span data-ttu-id="0a860-675">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="0a860-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="0a860-676">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="0a860-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0a860-677">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="0a860-677">Specify return values.</span></span>
> * <span data-ttu-id="0a860-678">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0a860-679">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0a860-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0a860-680">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="0a860-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="0a860-681">Información general (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-681">Overview 2.1</span></span>

<span data-ttu-id="0a860-682">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="0a860-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a860-683">API</span><span class="sxs-lookup"><span data-stu-id="0a860-683">API</span></span> | <span data-ttu-id="0a860-684">Descripción</span><span class="sxs-lookup"><span data-stu-id="0a860-684">Description</span></span> | <span data-ttu-id="0a860-685">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="0a860-685">Request body</span></span> | <span data-ttu-id="0a860-686">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="0a860-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0a860-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0a860-687">GET /api/TodoItems</span></span> | <span data-ttu-id="0a860-688">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-688">Get all to-do items</span></span> | <span data-ttu-id="0a860-689">None</span><span class="sxs-lookup"><span data-stu-id="0a860-689">None</span></span> | <span data-ttu-id="0a860-690">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="0a860-690">Array of to-do items</span></span>|
|<span data-ttu-id="0a860-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0a860-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a860-692">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="0a860-692">Get an item by ID</span></span> | <span data-ttu-id="0a860-693">None</span><span class="sxs-lookup"><span data-stu-id="0a860-693">None</span></span> | <span data-ttu-id="0a860-694">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-694">To-do item</span></span>|
|<span data-ttu-id="0a860-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0a860-695">POST /api/TodoItems</span></span> | <span data-ttu-id="0a860-696">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="0a860-696">Add a new item</span></span> | <span data-ttu-id="0a860-697">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-697">To-do item</span></span> | <span data-ttu-id="0a860-698">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-698">To-do item</span></span> |
|<span data-ttu-id="0a860-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0a860-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a860-700">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a860-701">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="0a860-701">To-do item</span></span> | <span data-ttu-id="0a860-702">None</span><span class="sxs-lookup"><span data-stu-id="0a860-702">None</span></span> |
|<span data-ttu-id="0a860-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-704">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a860-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a860-705">None</span><span class="sxs-lookup"><span data-stu-id="0a860-705">None</span></span> | <span data-ttu-id="0a860-706">None</span><span class="sxs-lookup"><span data-stu-id="0a860-706">None</span></span>|

<span data-ttu-id="0a860-707">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-707">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="0a860-713">Requisitos previos (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-716">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="0a860-717">Creación de un proyecto web (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-719">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="0a860-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a860-720">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a860-721">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a860-722">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0a860-723">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="0a860-724">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="0a860-724">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-727">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0a860-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a860-728">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a860-729">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0a860-730">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="0a860-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0a860-731">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-732">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-733">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="0a860-733">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a860-735">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a860-736">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="0a860-737">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="0a860-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="0a860-738">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="0a860-738">Select **Next**.</span></span>

* <span data-ttu-id="0a860-739">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="0a860-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="0a860-741">Prueba de la API (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-741">Test the API 2.1</span></span>

<span data-ttu-id="0a860-742">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="0a860-742">The project template creates a `values` API.</span></span> <span data-ttu-id="0a860-743">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a860-745">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a860-746">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a860-747">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a860-748">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="0a860-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a860-750">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a860-751">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="0a860-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-752">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a860-753">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a860-754">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a860-755">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="0a860-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a860-756">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="0a860-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0a860-757">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="0a860-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="0a860-758">Incorporación de una clase de modelo (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-758">Add a model class 2.1</span></span>

<span data-ttu-id="0a860-759">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="0a860-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a860-760">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="0a860-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-762">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0a860-763">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-764">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a860-765">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-766">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a860-767">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a860-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a860-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a860-769">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a860-770">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a860-771">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a860-772">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-772">Right-click the project.</span></span> <span data-ttu-id="0a860-773">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a860-774">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-774">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a860-776">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="0a860-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a860-777">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0a860-778">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0a860-779">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="0a860-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a860-780">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="0a860-781">Incorporación de un contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-781">Add a database context 2.1</span></span>

<span data-ttu-id="0a860-782">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a860-783">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0a860-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-785">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="0a860-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a860-786">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-787">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a860-788">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="0a860-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a860-789">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="0a860-790">Registro del contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-790">Register the database context 2.1</span></span>

<span data-ttu-id="0a860-791">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a860-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a860-792">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="0a860-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a860-793">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="0a860-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0a860-794">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="0a860-794">The preceding code:</span></span>

* <span data-ttu-id="0a860-795">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="0a860-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a860-796">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="0a860-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a860-797">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="0a860-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="0a860-798">Incorporación de un controlador (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-800">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="0a860-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a860-801">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="0a860-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="0a860-802">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="0a860-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0a860-803">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-805">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a860-806">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="0a860-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0a860-807">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0a860-808">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="0a860-808">The preceding code:</span></span>

* <span data-ttu-id="0a860-809">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="0a860-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0a860-810">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0a860-811">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a860-812">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0a860-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a860-813">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a860-814">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0a860-815">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="0a860-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0a860-816">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0a860-817">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="0a860-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0a860-818">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="0a860-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="0a860-819">Incorporación de métodos Get (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-819">Add Get methods 2.1</span></span>

<span data-ttu-id="0a860-820">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="0a860-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0a860-821">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="0a860-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0a860-822">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="0a860-822">Stop the app if it's still running.</span></span> <span data-ttu-id="0a860-823">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="0a860-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0a860-824">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0a860-825">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0a860-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0a860-826">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="0a860-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="0a860-827">Enrutamiento y rutas URL (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="0a860-828">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="0a860-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a860-829">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="0a860-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a860-830">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="0a860-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0a860-831">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="0a860-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a860-832">En este ejemplo, el nombre de clase de controlador es **Todo** Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="0a860-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0a860-833">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0a860-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a860-834">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="0a860-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a860-835">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="0a860-835">This sample doesn't use a template.</span></span> <span data-ttu-id="0a860-836">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a860-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a860-837">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a860-838">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="0a860-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="0a860-839">Valores devueltos (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-839">Return values 2.1</span></span>

<span data-ttu-id="0a860-840">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0a860-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a860-841">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a860-842">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="0a860-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a860-843">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="0a860-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a860-844">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a860-845">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="0a860-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a860-846">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="0a860-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0a860-847">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="0a860-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a860-848">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="0a860-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="0a860-849">Prueba del método GetTodoItems (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="0a860-850">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a860-851">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0a860-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0a860-852">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="0a860-852">Start the web app.</span></span>
* <span data-ttu-id="0a860-853">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-853">Start Postman.</span></span>
* <span data-ttu-id="0a860-854">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a860-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a860-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a860-856">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a860-857">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a860-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a860-858">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="0a860-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="0a860-859">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="0a860-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0a860-860">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="0a860-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0a860-861">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-861">Create a new request.</span></span>
  * <span data-ttu-id="0a860-862">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="0a860-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="0a860-863">Establezca el URI de solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="0a860-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0a860-864">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="0a860-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0a860-865">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="0a860-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a860-866">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-866">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="0a860-868">Incorporación de un método Create (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-868">Add a Create method 2.1</span></span>

<span data-ttu-id="0a860-869">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a860-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0a860-870">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0a860-871">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a860-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a860-872">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="0a860-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0a860-873">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0a860-874">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="0a860-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a860-875">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="0a860-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0a860-876">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="0a860-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0a860-877">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a860-878">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="0a860-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a860-879">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="0a860-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="0a860-880">Prueba del método PostTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="0a860-881">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-881">Build the project.</span></span>
* <span data-ttu-id="0a860-882">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="0a860-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a860-883">Establezca el URI en `https://localhost:<port>/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="0a860-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="0a860-884">Por ejemplo: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="0a860-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="0a860-885">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="0a860-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a860-886">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="0a860-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a860-887">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="0a860-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a860-888">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a860-889">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-889">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="0a860-891">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0a860-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="0a860-892">Prueba del URI del encabezado de ubicación (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="0a860-893">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="0a860-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a860-894">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="0a860-894">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0a860-896">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="0a860-896">Set the method to GET.</span></span>
* <span data-ttu-id="0a860-897">Establezca el URI en `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="0a860-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="0a860-898">Por ejemplo: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="0a860-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="0a860-899">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="0a860-900">Incorporación de un método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="0a860-901">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0a860-902">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a860-903">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a860-904">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="0a860-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a860-905">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="0a860-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a860-906">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="0a860-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="0a860-907">Prueba del método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="0a860-908">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0a860-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a860-909">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a860-910">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="0a860-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a860-911">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="0a860-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a860-912">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="0a860-912">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="0a860-914">Incorporación de un método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0a860-915">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="0a860-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0a860-916">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a860-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="0a860-917">Prueba del método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0a860-918">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="0a860-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a860-919">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="0a860-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a860-920">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="0a860-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0a860-921">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="0a860-921">Select **Send**.</span></span>

<span data-ttu-id="0a860-922">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="0a860-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0a860-923">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="0a860-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="0a860-924">Llamada a la API web con JavaScript (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="0a860-925">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0a860-926">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-926">jQuery initiates the request.</span></span> <span data-ttu-id="0a860-927">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0a860-928">Configure la aplicación para [atender archivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) y [habilitar la asignación de archivos predeterminada](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="0a860-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0a860-929">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0a860-930">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0a860-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a860-931">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="0a860-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0a860-932">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0a860-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a860-933">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="0a860-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0a860-934">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="0a860-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0a860-935">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0a860-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="0a860-936">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0a860-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0a860-937">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="0a860-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0a860-938">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="0a860-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="0a860-939">Obtención de una lista de tareas pendientes (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="0a860-940">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="0a860-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0a860-941">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="0a860-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0a860-942">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="0a860-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="0a860-943">Incorporación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="0a860-944">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="0a860-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0a860-945">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="0a860-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0a860-946">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="0a860-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0a860-947">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="0a860-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="0a860-948">Actualización de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="0a860-949">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="0a860-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0a860-950">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="0a860-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="0a860-951">Eliminación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="0a860-952">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="0a860-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="0a860-953">Incorporación de compatibilidad de autenticación a una API web (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="0a860-954">Recursos adicionales (2.1)</span><span class="sxs-lookup"><span data-stu-id="0a860-954">Additional resources 2.1</span></span>

<span data-ttu-id="0a860-955">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="0a860-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0a860-956">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0a860-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0a860-957">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="0a860-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0a860-958">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="0a860-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
