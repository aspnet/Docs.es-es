---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212618"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="5e43b-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e43b-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="5e43b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="5e43b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="5e43b-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e43b-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e43b-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="5e43b-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5e43b-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-107">Create a web API project.</span></span>
> * <span data-ttu-id="5e43b-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5e43b-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="5e43b-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="5e43b-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="5e43b-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-111">Call the web API with Postman.</span></span>

<span data-ttu-id="5e43b-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="5e43b-113">Información general</span><span class="sxs-lookup"><span data-stu-id="5e43b-113">Overview</span></span>

<span data-ttu-id="5e43b-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="5e43b-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5e43b-115">API</span><span class="sxs-lookup"><span data-stu-id="5e43b-115">API</span></span> | <span data-ttu-id="5e43b-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="5e43b-116">Description</span></span> | <span data-ttu-id="5e43b-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="5e43b-117">Request body</span></span> | <span data-ttu-id="5e43b-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="5e43b-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="5e43b-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="5e43b-119">Get all to-do items</span></span> | <span data-ttu-id="5e43b-120">Ninguna</span><span class="sxs-lookup"><span data-stu-id="5e43b-120">None</span></span> | <span data-ttu-id="5e43b-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="5e43b-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="5e43b-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="5e43b-122">Get an item by ID</span></span> | <span data-ttu-id="5e43b-123">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-123">None</span></span> | <span data-ttu-id="5e43b-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="5e43b-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="5e43b-125">Add a new item</span></span> | <span data-ttu-id="5e43b-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-126">To-do item</span></span> | <span data-ttu-id="5e43b-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="5e43b-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5e43b-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-129">To-do item</span></span> | <span data-ttu-id="5e43b-130">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-130">None</span></span> |
|<span data-ttu-id="5e43b-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="5e43b-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5e43b-133">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-133">None</span></span> | <span data-ttu-id="5e43b-134">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-134">None</span></span>|

<span data-ttu-id="5e43b-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5e43b-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5e43b-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5e43b-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="5e43b-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5e43b-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5e43b-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5e43b-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="5e43b-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5e43b-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5e43b-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5e43b-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5e43b-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="5e43b-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="5e43b-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="5e43b-158">The preceding commands:</span></span>

  * <span data-ttu-id="5e43b-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5e43b-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="5e43b-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5e43b-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5e43b-164">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="5e43b-165">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="5e43b-167">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), seleccione la **plataforma de destino** .NET Core 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="5e43b-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-168">Select **Next**.</span></span>

* <span data-ttu-id="5e43b-169">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="5e43b-171">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="5e43b-172">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="5e43b-172">Test the API</span></span>

<span data-ttu-id="5e43b-173">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="5e43b-174">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e43b-176">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5e43b-177">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5e43b-178">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5e43b-179">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5e43b-181">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5e43b-182">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-183">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e43b-184">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5e43b-185">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5e43b-186">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="5e43b-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5e43b-187">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="5e43b-188">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="5e43b-189">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="5e43b-189">Add a model class</span></span>

<span data-ttu-id="5e43b-190">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="5e43b-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5e43b-191">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="5e43b-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-193">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5e43b-194">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e43b-195">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="5e43b-196">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e43b-197">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5e43b-198">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5e43b-200">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5e43b-201">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5e43b-203">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-203">Right-click the project.</span></span> <span data-ttu-id="5e43b-204">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e43b-205">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-205">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5e43b-207">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5e43b-208">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5e43b-209">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="5e43b-210">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="5e43b-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5e43b-211">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5e43b-212">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="5e43b-212">Add a database context</span></span>

<span data-ttu-id="5e43b-213">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5e43b-214">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="5e43b-216">Adición de Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="5e43b-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="5e43b-217">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="5e43b-218">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="5e43b-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="5e43b-219">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="5e43b-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="5e43b-220">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="5e43b-221">Siga las instrucciones anteriores para agregar el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="5e43b-223">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="5e43b-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="5e43b-224">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e43b-225">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-226">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5e43b-227">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5e43b-228">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="5e43b-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5e43b-229">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="5e43b-229">Register the database context</span></span>

<span data-ttu-id="5e43b-230">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5e43b-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5e43b-231">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="5e43b-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="5e43b-232">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="5e43b-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="5e43b-233">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e43b-233">The preceding code:</span></span>

* <span data-ttu-id="5e43b-234">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5e43b-235">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="5e43b-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5e43b-236">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="5e43b-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="5e43b-237">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="5e43b-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-239">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5e43b-240">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5e43b-241">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="5e43b-242">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="5e43b-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="5e43b-243">Seleccione **TodoItem (TodoApi.Models)** en **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="5e43b-244">Seleccione **TodoContext (TodoApi.Models)** en **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="5e43b-245">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-246">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5e43b-247">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="5e43b-248">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="5e43b-248">The preceding commands:</span></span>

* <span data-ttu-id="5e43b-249">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5e43b-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="5e43b-250">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="5e43b-251">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="5e43b-252">El código generado:</span><span class="sxs-lookup"><span data-stu-id="5e43b-252">The generated code:</span></span>

* <span data-ttu-id="5e43b-253">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5e43b-254">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5e43b-255">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="5e43b-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5e43b-256">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5e43b-257">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="5e43b-258">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="5e43b-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="5e43b-259">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="5e43b-260">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="5e43b-261">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="5e43b-262">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="5e43b-263">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="5e43b-264">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="5e43b-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="5e43b-265">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5e43b-266">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e43b-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5e43b-267">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="5e43b-268">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="5e43b-269">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5e43b-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5e43b-270">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="5e43b-271">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="5e43b-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="5e43b-272">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5e43b-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5e43b-273">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5e43b-274">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="5e43b-275">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="5e43b-275">Install Postman</span></span>

<span data-ttu-id="5e43b-276">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5e43b-277">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="5e43b-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="5e43b-278">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-278">Start the web app.</span></span>
* <span data-ttu-id="5e43b-279">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-279">Start Postman.</span></span>
* <span data-ttu-id="5e43b-280">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="5e43b-281">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="5e43b-282">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="5e43b-283">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="5e43b-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="5e43b-284">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="5e43b-284">Create a new request.</span></span>
* <span data-ttu-id="5e43b-285">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5e43b-286">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="5e43b-287">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="5e43b-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5e43b-288">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="5e43b-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5e43b-289">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5e43b-290">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-290">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5e43b-292">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="5e43b-292">Test the location header URI</span></span>

* <span data-ttu-id="5e43b-293">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5e43b-294">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="5e43b-294">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="5e43b-296">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="5e43b-296">Set the method to GET.</span></span>
* <span data-ttu-id="5e43b-297">Pegue el URI (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5e43b-298">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="5e43b-299">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="5e43b-299">Examine the GET methods</span></span>

<span data-ttu-id="5e43b-300">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="5e43b-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="5e43b-301">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="5e43b-302">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5e43b-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="5e43b-303">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="5e43b-304">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="5e43b-304">Test Get with Postman</span></span>

* <span data-ttu-id="5e43b-305">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="5e43b-305">Create a new request.</span></span>
* <span data-ttu-id="5e43b-306">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="5e43b-307">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="5e43b-308">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="5e43b-309">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5e43b-310">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-310">Select **Send**.</span></span>

<span data-ttu-id="5e43b-311">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="5e43b-311">This app uses an in-memory database.</span></span> <span data-ttu-id="5e43b-312">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="5e43b-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="5e43b-313">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="5e43b-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="5e43b-314">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="5e43b-314">Routing and URL paths</span></span>

<span data-ttu-id="5e43b-315">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5e43b-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5e43b-316">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="5e43b-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5e43b-317">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="5e43b-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="5e43b-318">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="5e43b-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5e43b-319">En este ejemplo, el nombre de clase de controlador es **TodoItems**Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="5e43b-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="5e43b-320">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5e43b-321">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="5e43b-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5e43b-322">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="5e43b-322">This sample doesn't use a template.</span></span> <span data-ttu-id="5e43b-323">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5e43b-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5e43b-324">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5e43b-325">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5e43b-326">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="5e43b-326">Return values</span></span>

<span data-ttu-id="5e43b-327">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5e43b-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5e43b-328">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5e43b-329">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="5e43b-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5e43b-330">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="5e43b-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5e43b-331">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e43b-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5e43b-332">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5e43b-333">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="5e43b-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5e43b-334">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="5e43b-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5e43b-335">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5e43b-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="5e43b-336">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-336">The PutTodoItem method</span></span>

<span data-ttu-id="5e43b-337">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="5e43b-338">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5e43b-339">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5e43b-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5e43b-340">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="5e43b-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5e43b-341">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5e43b-342">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5e43b-343">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="5e43b-344">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5e43b-345">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5e43b-346">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5e43b-347">Actualice el elemento to-do que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="5e43b-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5e43b-348">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="5e43b-348">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="5e43b-350">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="5e43b-351">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5e43b-352">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5e43b-353">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5e43b-354">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5e43b-355">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5e43b-356">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="5e43b-357">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="5e43b-357">Prevent over-posting</span></span>

<span data-ttu-id="5e43b-358">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="5e43b-359">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="5e43b-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="5e43b-360">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="5e43b-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="5e43b-361">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="5e43b-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="5e43b-362">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="5e43b-363">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="5e43b-363">A DTO may be used to:</span></span>

* <span data-ttu-id="5e43b-364">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-364">Prevent over-posting.</span></span>
* <span data-ttu-id="5e43b-365">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="5e43b-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="5e43b-366">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="5e43b-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="5e43b-367">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="5e43b-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="5e43b-368">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="5e43b-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="5e43b-369">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="5e43b-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="5e43b-370">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="5e43b-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="5e43b-371">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="5e43b-372">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="5e43b-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="5e43b-373">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="5e43b-374">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5e43b-375">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="5e43b-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="5e43b-376">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="5e43b-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5e43b-377">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="5e43b-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5e43b-378">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-378">Create a web API project.</span></span>
> * <span data-ttu-id="5e43b-379">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5e43b-380">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-380">Add a controller.</span></span>
> * <span data-ttu-id="5e43b-381">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="5e43b-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="5e43b-382">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5e43b-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="5e43b-383">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-383">Specify return values.</span></span>
> * <span data-ttu-id="5e43b-384">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="5e43b-385">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5e43b-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="5e43b-386">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="5e43b-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="5e43b-387">Información general</span><span class="sxs-lookup"><span data-stu-id="5e43b-387">Overview</span></span>

<span data-ttu-id="5e43b-388">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="5e43b-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5e43b-389">API</span><span class="sxs-lookup"><span data-stu-id="5e43b-389">API</span></span> | <span data-ttu-id="5e43b-390">Descripción</span><span class="sxs-lookup"><span data-stu-id="5e43b-390">Description</span></span> | <span data-ttu-id="5e43b-391">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="5e43b-391">Request body</span></span> | <span data-ttu-id="5e43b-392">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="5e43b-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="5e43b-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5e43b-393">GET /api/TodoItems</span></span> | <span data-ttu-id="5e43b-394">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="5e43b-394">Get all to-do items</span></span> | <span data-ttu-id="5e43b-395">Ninguna</span><span class="sxs-lookup"><span data-stu-id="5e43b-395">None</span></span> | <span data-ttu-id="5e43b-396">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="5e43b-396">Array of to-do items</span></span>|
|<span data-ttu-id="5e43b-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5e43b-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="5e43b-398">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="5e43b-398">Get an item by ID</span></span> | <span data-ttu-id="5e43b-399">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-399">None</span></span> | <span data-ttu-id="5e43b-400">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-400">To-do item</span></span>|
|<span data-ttu-id="5e43b-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5e43b-401">POST /api/TodoItems</span></span> | <span data-ttu-id="5e43b-402">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="5e43b-402">Add a new item</span></span> | <span data-ttu-id="5e43b-403">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-403">To-do item</span></span> | <span data-ttu-id="5e43b-404">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-404">To-do item</span></span> |
|<span data-ttu-id="5e43b-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5e43b-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="5e43b-406">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5e43b-407">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-407">To-do item</span></span> | <span data-ttu-id="5e43b-408">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-408">None</span></span> |
|<span data-ttu-id="5e43b-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="5e43b-410">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5e43b-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5e43b-411">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-411">None</span></span> | <span data-ttu-id="5e43b-412">None</span><span class="sxs-lookup"><span data-stu-id="5e43b-412">None</span></span>|

<span data-ttu-id="5e43b-413">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-413">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5e43b-419">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5e43b-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-422">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5e43b-423">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="5e43b-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-425">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5e43b-426">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5e43b-427">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5e43b-428">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="5e43b-429">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="5e43b-430">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-430">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5e43b-433">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5e43b-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5e43b-434">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5e43b-435">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="5e43b-436">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="5e43b-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="5e43b-437">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5e43b-439">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-439">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5e43b-441">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="5e43b-442">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="5e43b-443">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), seleccione la **plataforma de destino** .NET Core 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="5e43b-444">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-444">Select **Next**.</span></span>

* <span data-ttu-id="5e43b-445">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="5e43b-447">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="5e43b-447">Test the API</span></span>

<span data-ttu-id="5e43b-448">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-448">The project template creates a `values` API.</span></span> <span data-ttu-id="5e43b-449">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e43b-451">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5e43b-452">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5e43b-453">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5e43b-454">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5e43b-456">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5e43b-457">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-458">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e43b-459">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5e43b-460">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5e43b-461">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="5e43b-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5e43b-462">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="5e43b-463">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="5e43b-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="5e43b-464">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="5e43b-464">Add a model class</span></span>

<span data-ttu-id="5e43b-465">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="5e43b-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5e43b-466">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="5e43b-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-468">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5e43b-469">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e43b-470">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="5e43b-471">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e43b-472">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5e43b-473">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e43b-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43b-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5e43b-475">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5e43b-476">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5e43b-478">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-478">Right-click the project.</span></span> <span data-ttu-id="5e43b-479">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e43b-480">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-480">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5e43b-482">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5e43b-483">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5e43b-484">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5e43b-485">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="5e43b-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5e43b-486">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5e43b-487">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="5e43b-487">Add a database context</span></span>

<span data-ttu-id="5e43b-488">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5e43b-489">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-491">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e43b-492">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-493">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5e43b-494">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5e43b-495">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5e43b-496">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="5e43b-496">Register the database context</span></span>

<span data-ttu-id="5e43b-497">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5e43b-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5e43b-498">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="5e43b-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="5e43b-499">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="5e43b-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="5e43b-500">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e43b-500">The preceding code:</span></span>

* <span data-ttu-id="5e43b-501">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5e43b-502">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="5e43b-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5e43b-503">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="5e43b-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="5e43b-504">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="5e43b-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-506">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5e43b-507">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="5e43b-508">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="5e43b-509">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-511">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5e43b-512">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="5e43b-513">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="5e43b-514">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e43b-514">The preceding code:</span></span>

* <span data-ttu-id="5e43b-515">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="5e43b-516">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5e43b-517">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5e43b-518">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="5e43b-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5e43b-519">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5e43b-520">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="5e43b-521">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="5e43b-522">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e43b-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="5e43b-523">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="5e43b-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="5e43b-524">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="5e43b-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="5e43b-525">Incorporación de métodos Get</span><span class="sxs-lookup"><span data-stu-id="5e43b-525">Add Get methods</span></span>

<span data-ttu-id="5e43b-526">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="5e43b-527">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="5e43b-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="5e43b-528">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="5e43b-528">Stop the app if it's still running.</span></span> <span data-ttu-id="5e43b-529">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="5e43b-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="5e43b-530">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="5e43b-531">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5e43b-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="5e43b-532">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="5e43b-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="5e43b-533">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="5e43b-533">Routing and URL paths</span></span>

<span data-ttu-id="5e43b-534">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5e43b-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5e43b-535">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="5e43b-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5e43b-536">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="5e43b-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="5e43b-537">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="5e43b-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5e43b-538">En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="5e43b-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="5e43b-539">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5e43b-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5e43b-540">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="5e43b-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5e43b-541">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="5e43b-541">This sample doesn't use a template.</span></span> <span data-ttu-id="5e43b-542">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5e43b-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5e43b-543">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5e43b-544">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5e43b-545">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="5e43b-545">Return values</span></span>

<span data-ttu-id="5e43b-546">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5e43b-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5e43b-547">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5e43b-548">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="5e43b-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5e43b-549">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="5e43b-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5e43b-550">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e43b-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5e43b-551">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5e43b-552">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="5e43b-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5e43b-553">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="5e43b-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5e43b-554">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5e43b-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="5e43b-555">Prueba del método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="5e43b-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="5e43b-556">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5e43b-557">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="5e43b-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="5e43b-558">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-558">Start the web app.</span></span>
* <span data-ttu-id="5e43b-559">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-559">Start Postman.</span></span>
* <span data-ttu-id="5e43b-560">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e43b-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e43b-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e43b-562">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5e43b-563">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e43b-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5e43b-564">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="5e43b-565">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="5e43b-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="5e43b-566">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="5e43b-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="5e43b-567">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="5e43b-567">Create a new request.</span></span>
  * <span data-ttu-id="5e43b-568">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="5e43b-569">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="5e43b-570">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="5e43b-571">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="5e43b-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5e43b-572">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-572">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="5e43b-574">Incorporación de un método Create</span><span class="sxs-lookup"><span data-stu-id="5e43b-574">Add a Create method</span></span>

<span data-ttu-id="5e43b-575">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e43b-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="5e43b-576">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5e43b-577">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e43b-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5e43b-578">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="5e43b-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="5e43b-579">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="5e43b-580">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5e43b-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5e43b-581">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5e43b-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="5e43b-582">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="5e43b-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="5e43b-583">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5e43b-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5e43b-584">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5e43b-585">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="5e43b-586">Prueba del método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="5e43b-587">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-587">Build the project.</span></span>
* <span data-ttu-id="5e43b-588">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5e43b-589">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="5e43b-590">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="5e43b-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5e43b-591">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="5e43b-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5e43b-592">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5e43b-593">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-593">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="5e43b-595">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5e43b-596">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="5e43b-596">Test the location header URI</span></span>

* <span data-ttu-id="5e43b-597">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5e43b-598">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="5e43b-598">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="5e43b-600">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="5e43b-600">Set the method to GET.</span></span>
* <span data-ttu-id="5e43b-601">Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="5e43b-602">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="5e43b-603">Incorporación de un método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="5e43b-604">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="5e43b-605">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5e43b-606">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5e43b-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5e43b-607">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="5e43b-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5e43b-608">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5e43b-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5e43b-609">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5e43b-610">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="5e43b-611">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5e43b-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5e43b-612">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5e43b-613">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e43b-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5e43b-614">Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="5e43b-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5e43b-615">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="5e43b-615">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="5e43b-617">Incorporación de un método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="5e43b-618">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e43b-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="5e43b-619">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5e43b-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5e43b-620">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e43b-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5e43b-621">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5e43b-622">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5e43b-623">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="5e43b-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="5e43b-624">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e43b-624">Select **Send**.</span></span>

<span data-ttu-id="5e43b-625">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="5e43b-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="5e43b-626">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="5e43b-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5e43b-627">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="5e43b-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="5e43b-628">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="5e43b-629">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="5e43b-629">jQuery initiates the request.</span></span> <span data-ttu-id="5e43b-630">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="5e43b-631">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="5e43b-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="5e43b-632">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="5e43b-633">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="5e43b-634">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="5e43b-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="5e43b-635">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="5e43b-636">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="5e43b-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="5e43b-637">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="5e43b-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="5e43b-638">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5e43b-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="5e43b-639">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="5e43b-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="5e43b-640">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="5e43b-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="5e43b-641">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="5e43b-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="5e43b-642">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="5e43b-642">Get a list of to-do items</span></span>

<span data-ttu-id="5e43b-643">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="5e43b-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="5e43b-644">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="5e43b-645">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="5e43b-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="5e43b-646">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-646">Add a to-do item</span></span>

<span data-ttu-id="5e43b-647">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="5e43b-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="5e43b-648">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="5e43b-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="5e43b-649">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="5e43b-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="5e43b-650">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="5e43b-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="5e43b-651">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-651">Update a to-do item</span></span>

<span data-ttu-id="5e43b-652">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="5e43b-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="5e43b-653">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="5e43b-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="5e43b-654">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="5e43b-654">Delete a to-do item</span></span>

<span data-ttu-id="5e43b-655">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5e43b-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="5e43b-656">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="5e43b-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources"></a>[!INCLUDE[](~/includes/Identity<span data-ttu-id="5e43b-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="5e43b-657">Server4.md)]</span></span>

<span data-ttu-id="5e43b-658">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5e43b-658">Additional resources</span></span> <span data-ttu-id="5e43b-659">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="5e43b-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span>

<span data-ttu-id="5e43b-660">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5e43b-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* <span data-ttu-id="5e43b-661">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="5e43b-661">For more information, see the following resources:</span></span>
