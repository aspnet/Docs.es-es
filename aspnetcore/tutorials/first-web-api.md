---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 63f91086a7e9d71add7f7a5d58d96f46fa76353c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407790"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4a35e-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a35e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4a35e-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4a35e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4a35e-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a35e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4a35e-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="4a35e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a35e-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-107">Create a web API project.</span></span>
> * <span data-ttu-id="4a35e-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4a35e-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="4a35e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4a35e-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4a35e-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4a35e-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4a35e-113">Información general</span><span class="sxs-lookup"><span data-stu-id="4a35e-113">Overview</span></span>

<span data-ttu-id="4a35e-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="4a35e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4a35e-115">API</span><span class="sxs-lookup"><span data-stu-id="4a35e-115">API</span></span> | <span data-ttu-id="4a35e-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="4a35e-116">Description</span></span> | <span data-ttu-id="4a35e-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="4a35e-117">Request body</span></span> | <span data-ttu-id="4a35e-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="4a35e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="4a35e-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="4a35e-119">Get all to-do items</span></span> | <span data-ttu-id="4a35e-120">Ninguna</span><span class="sxs-lookup"><span data-stu-id="4a35e-120">None</span></span> | <span data-ttu-id="4a35e-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="4a35e-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="4a35e-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="4a35e-122">Get an item by ID</span></span> | <span data-ttu-id="4a35e-123">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-123">None</span></span> | <span data-ttu-id="4a35e-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="4a35e-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="4a35e-125">Add a new item</span></span> | <span data-ttu-id="4a35e-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-126">To-do item</span></span> | <span data-ttu-id="4a35e-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="4a35e-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4a35e-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-129">To-do item</span></span> | <span data-ttu-id="4a35e-130">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-130">None</span></span> |
|<span data-ttu-id="4a35e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="4a35e-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4a35e-133">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-133">None</span></span> | <span data-ttu-id="4a35e-134">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-134">None</span></span>|

<span data-ttu-id="4a35e-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4a35e-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4a35e-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4a35e-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="4a35e-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4a35e-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4a35e-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4a35e-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="4a35e-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a35e-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4a35e-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4a35e-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4a35e-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4a35e-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4a35e-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="4a35e-158">The preceding commands:</span></span>

  * <span data-ttu-id="4a35e-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4a35e-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4a35e-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a35e-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4a35e-164">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4a35e-165">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="4a35e-167">Confirme que **Plataforma de destino** está establecido en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="4a35e-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-168">Select **Next**.</span></span>

  ![Selección de .NET Core 3.1 de macOS](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="4a35e-170">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4a35e-172">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4a35e-173">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="4a35e-173">Test the API</span></span>

<span data-ttu-id="4a35e-174">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4a35e-175">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a35e-177">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4a35e-178">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4a35e-179">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4a35e-180">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4a35e-182">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4a35e-183">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-184">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a35e-185">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4a35e-186">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4a35e-187">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="4a35e-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4a35e-188">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4a35e-189">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4a35e-190">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="4a35e-190">Add a model class</span></span>

<span data-ttu-id="4a35e-191">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="4a35e-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4a35e-192">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="4a35e-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-194">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4a35e-195">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4a35e-196">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="4a35e-197">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4a35e-198">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4a35e-199">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a35e-201">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4a35e-202">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-203">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a35e-204">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-204">Right-click the project.</span></span> <span data-ttu-id="4a35e-205">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4a35e-206">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-206">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4a35e-208">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4a35e-209">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4a35e-210">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="4a35e-211">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="4a35e-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4a35e-212">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4a35e-213">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a35e-213">Add a database context</span></span>

<span data-ttu-id="4a35e-214">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4a35e-215">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4a35e-217">Adición de Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4a35e-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4a35e-218">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4a35e-219">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4a35e-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4a35e-220">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="4a35e-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="4a35e-221">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4a35e-222">Siga las instrucciones anteriores para agregar el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4a35e-224">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="4a35e-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="4a35e-225">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4a35e-226">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-227">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4a35e-228">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4a35e-229">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4a35e-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4a35e-230">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a35e-230">Register the database context</span></span>

<span data-ttu-id="4a35e-231">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a35e-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4a35e-232">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="4a35e-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="4a35e-233">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="4a35e-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4a35e-234">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4a35e-234">The preceding code:</span></span>

* <span data-ttu-id="4a35e-235">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4a35e-236">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="4a35e-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4a35e-237">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="4a35e-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4a35e-238">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="4a35e-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-240">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4a35e-241">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4a35e-242">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4a35e-243">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="4a35e-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4a35e-244">Seleccione **TodoItem (TodoApi.Models)** en **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4a35e-245">Seleccione **TodoContext (TodoApi.Models)** en **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4a35e-246">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4a35e-248">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4a35e-249">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="4a35e-249">The preceding commands:</span></span>

* <span data-ttu-id="4a35e-250">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="4a35e-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4a35e-251">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4a35e-252">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4a35e-253">El código generado:</span><span class="sxs-lookup"><span data-stu-id="4a35e-253">The generated code:</span></span>

* <span data-ttu-id="4a35e-254">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4a35e-255">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4a35e-256">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4a35e-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4a35e-257">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4a35e-258">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="4a35e-259">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="4a35e-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="4a35e-260">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="4a35e-261">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="4a35e-262">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="4a35e-263">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4a35e-264">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4a35e-265">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="4a35e-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4a35e-266">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4a35e-267">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a35e-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4a35e-268">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4a35e-269">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4a35e-270">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4a35e-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4a35e-271">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4a35e-272">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="4a35e-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4a35e-273">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4a35e-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4a35e-274">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4a35e-275">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4a35e-276">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="4a35e-276">Install Postman</span></span>

<span data-ttu-id="4a35e-277">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4a35e-278">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4a35e-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4a35e-279">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-279">Start the web app.</span></span>
* <span data-ttu-id="4a35e-280">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-280">Start Postman.</span></span>
* <span data-ttu-id="4a35e-281">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="4a35e-282">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4a35e-283">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4a35e-284">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="4a35e-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4a35e-285">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a35e-285">Create a new request.</span></span>
* <span data-ttu-id="4a35e-286">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4a35e-287">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="4a35e-288">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="4a35e-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4a35e-289">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="4a35e-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4a35e-290">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4a35e-291">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-291">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4a35e-293">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="4a35e-293">Test the location header URI</span></span>

* <span data-ttu-id="4a35e-294">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4a35e-295">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="4a35e-295">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="4a35e-297">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="4a35e-297">Set the method to GET.</span></span>
* <span data-ttu-id="4a35e-298">Pegue el URI (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4a35e-299">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4a35e-300">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="4a35e-300">Examine the GET methods</span></span>

<span data-ttu-id="4a35e-301">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="4a35e-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4a35e-302">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4a35e-303">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4a35e-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="4a35e-304">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4a35e-305">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="4a35e-305">Test Get with Postman</span></span>

* <span data-ttu-id="4a35e-306">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a35e-306">Create a new request.</span></span>
* <span data-ttu-id="4a35e-307">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4a35e-308">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4a35e-309">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4a35e-310">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4a35e-311">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-311">Select **Send**.</span></span>

<span data-ttu-id="4a35e-312">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="4a35e-312">This app uses an in-memory database.</span></span> <span data-ttu-id="4a35e-313">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="4a35e-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4a35e-314">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="4a35e-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4a35e-315">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="4a35e-315">Routing and URL paths</span></span>

<span data-ttu-id="4a35e-316">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4a35e-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4a35e-317">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="4a35e-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4a35e-318">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="4a35e-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4a35e-319">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="4a35e-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4a35e-320">En este ejemplo, el nombre de clase de controlador es **TodoItems**Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="4a35e-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4a35e-321">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4a35e-322">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4a35e-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4a35e-323">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="4a35e-323">This sample doesn't use a template.</span></span> <span data-ttu-id="4a35e-324">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4a35e-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4a35e-325">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4a35e-326">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4a35e-327">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="4a35e-327">Return values</span></span>

<span data-ttu-id="4a35e-328">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4a35e-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4a35e-329">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4a35e-330">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="4a35e-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4a35e-331">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="4a35e-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4a35e-332">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a35e-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4a35e-333">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4a35e-334">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="4a35e-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4a35e-335">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="4a35e-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4a35e-336">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4a35e-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4a35e-337">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-337">The PutTodoItem method</span></span>

<span data-ttu-id="4a35e-338">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4a35e-339">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4a35e-340">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4a35e-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4a35e-341">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="4a35e-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4a35e-342">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4a35e-343">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4a35e-344">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="4a35e-345">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4a35e-346">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4a35e-347">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4a35e-348">Actualice el elemento to-do que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="4a35e-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4a35e-349">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="4a35e-349">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4a35e-351">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="4a35e-352">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4a35e-353">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4a35e-354">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4a35e-355">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4a35e-356">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4a35e-357">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="4a35e-358">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="4a35e-358">Prevent over-posting</span></span>

<span data-ttu-id="4a35e-359">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="4a35e-360">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="4a35e-360">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="4a35e-361">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="4a35e-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="4a35e-362">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="4a35e-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="4a35e-363">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="4a35e-364">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="4a35e-364">A DTO may be used to:</span></span>

* <span data-ttu-id="4a35e-365">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-365">Prevent over-posting.</span></span>
* <span data-ttu-id="4a35e-366">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="4a35e-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="4a35e-367">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="4a35e-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="4a35e-368">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="4a35e-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="4a35e-369">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="4a35e-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="4a35e-370">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="4a35e-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="4a35e-371">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="4a35e-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="4a35e-372">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="4a35e-373">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="4a35e-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="4a35e-374">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="4a35e-375">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4a35e-376">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a35e-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="4a35e-377">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4a35e-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a35e-378">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="4a35e-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a35e-379">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-379">Create a web API project.</span></span>
> * <span data-ttu-id="4a35e-380">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4a35e-381">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-381">Add a controller.</span></span>
> * <span data-ttu-id="4a35e-382">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="4a35e-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="4a35e-383">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4a35e-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4a35e-384">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-384">Specify return values.</span></span>
> * <span data-ttu-id="4a35e-385">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4a35e-386">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a35e-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4a35e-387">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="4a35e-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4a35e-388">Información general</span><span class="sxs-lookup"><span data-stu-id="4a35e-388">Overview</span></span>

<span data-ttu-id="4a35e-389">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="4a35e-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4a35e-390">API</span><span class="sxs-lookup"><span data-stu-id="4a35e-390">API</span></span> | <span data-ttu-id="4a35e-391">Descripción</span><span class="sxs-lookup"><span data-stu-id="4a35e-391">Description</span></span> | <span data-ttu-id="4a35e-392">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="4a35e-392">Request body</span></span> | <span data-ttu-id="4a35e-393">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="4a35e-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4a35e-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4a35e-394">GET /api/TodoItems</span></span> | <span data-ttu-id="4a35e-395">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="4a35e-395">Get all to-do items</span></span> | <span data-ttu-id="4a35e-396">Ninguna</span><span class="sxs-lookup"><span data-stu-id="4a35e-396">None</span></span> | <span data-ttu-id="4a35e-397">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="4a35e-397">Array of to-do items</span></span>|
|<span data-ttu-id="4a35e-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4a35e-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4a35e-399">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="4a35e-399">Get an item by ID</span></span> | <span data-ttu-id="4a35e-400">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-400">None</span></span> | <span data-ttu-id="4a35e-401">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-401">To-do item</span></span>|
|<span data-ttu-id="4a35e-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4a35e-402">POST /api/TodoItems</span></span> | <span data-ttu-id="4a35e-403">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="4a35e-403">Add a new item</span></span> | <span data-ttu-id="4a35e-404">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-404">To-do item</span></span> | <span data-ttu-id="4a35e-405">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-405">To-do item</span></span> |
|<span data-ttu-id="4a35e-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4a35e-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4a35e-407">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4a35e-408">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-408">To-do item</span></span> | <span data-ttu-id="4a35e-409">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-409">None</span></span> |
|<span data-ttu-id="4a35e-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4a35e-411">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4a35e-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4a35e-412">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-412">None</span></span> | <span data-ttu-id="4a35e-413">None</span><span class="sxs-lookup"><span data-stu-id="4a35e-413">None</span></span>|

<span data-ttu-id="4a35e-414">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-414">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4a35e-420">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4a35e-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-423">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4a35e-424">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="4a35e-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-426">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4a35e-427">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4a35e-428">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4a35e-429">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4a35e-430">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4a35e-431">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-431">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a35e-434">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4a35e-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4a35e-435">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4a35e-436">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4a35e-437">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="4a35e-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4a35e-438">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a35e-440">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-440">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4a35e-442">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4a35e-443">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="4a35e-444">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, acepte la **plataforma de destino** predeterminada de \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4a35e-445">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4a35e-447">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="4a35e-447">Test the API</span></span>

<span data-ttu-id="4a35e-448">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-448">The project template creates a `values` API.</span></span> <span data-ttu-id="4a35e-449">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a35e-451">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4a35e-452">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4a35e-453">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4a35e-454">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4a35e-456">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4a35e-457">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-458">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a35e-459">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4a35e-460">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4a35e-461">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="4a35e-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4a35e-462">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4a35e-463">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="4a35e-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4a35e-464">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="4a35e-464">Add a model class</span></span>

<span data-ttu-id="4a35e-465">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="4a35e-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4a35e-466">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="4a35e-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-468">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4a35e-469">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4a35e-470">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="4a35e-471">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4a35e-472">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4a35e-473">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4a35e-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a35e-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a35e-475">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4a35e-476">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4a35e-478">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-478">Right-click the project.</span></span> <span data-ttu-id="4a35e-479">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4a35e-480">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-480">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4a35e-482">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4a35e-483">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4a35e-484">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4a35e-485">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="4a35e-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4a35e-486">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4a35e-487">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a35e-487">Add a database context</span></span>

<span data-ttu-id="4a35e-488">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4a35e-489">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-491">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4a35e-492">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-493">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4a35e-494">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4a35e-495">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4a35e-496">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="4a35e-496">Register the database context</span></span>

<span data-ttu-id="4a35e-497">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a35e-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4a35e-498">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="4a35e-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="4a35e-499">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="4a35e-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4a35e-500">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4a35e-500">The preceding code:</span></span>

* <span data-ttu-id="4a35e-501">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4a35e-502">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="4a35e-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4a35e-503">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="4a35e-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4a35e-504">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="4a35e-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-506">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4a35e-507">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4a35e-508">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4a35e-509">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-511">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4a35e-512">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4a35e-513">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4a35e-514">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4a35e-514">The preceding code:</span></span>

* <span data-ttu-id="4a35e-515">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4a35e-516">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4a35e-517">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4a35e-518">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4a35e-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4a35e-519">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4a35e-520">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4a35e-521">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4a35e-522">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a35e-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4a35e-523">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="4a35e-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4a35e-524">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="4a35e-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4a35e-525">Incorporación de métodos Get</span><span class="sxs-lookup"><span data-stu-id="4a35e-525">Add Get methods</span></span>

<span data-ttu-id="4a35e-526">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4a35e-527">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="4a35e-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4a35e-528">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="4a35e-528">Stop the app if it's still running.</span></span> <span data-ttu-id="4a35e-529">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="4a35e-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4a35e-530">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4a35e-531">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4a35e-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4a35e-532">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="4a35e-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4a35e-533">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="4a35e-533">Routing and URL paths</span></span>

<span data-ttu-id="4a35e-534">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4a35e-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4a35e-535">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="4a35e-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4a35e-536">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="4a35e-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4a35e-537">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="4a35e-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4a35e-538">En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="4a35e-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4a35e-539">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4a35e-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4a35e-540">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="4a35e-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4a35e-541">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="4a35e-541">This sample doesn't use a template.</span></span> <span data-ttu-id="4a35e-542">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4a35e-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4a35e-543">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4a35e-544">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4a35e-545">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="4a35e-545">Return values</span></span>

<span data-ttu-id="4a35e-546">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4a35e-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4a35e-547">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4a35e-548">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="4a35e-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4a35e-549">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="4a35e-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4a35e-550">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a35e-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4a35e-551">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4a35e-552">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="4a35e-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4a35e-553">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="4a35e-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4a35e-554">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4a35e-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4a35e-555">Prueba del método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="4a35e-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="4a35e-556">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4a35e-557">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4a35e-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="4a35e-558">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-558">Start the web app.</span></span>
* <span data-ttu-id="4a35e-559">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-559">Start Postman.</span></span>
* <span data-ttu-id="4a35e-560">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a35e-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a35e-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a35e-562">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a35e-563">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4a35e-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4a35e-564">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4a35e-565">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="4a35e-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4a35e-566">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="4a35e-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4a35e-567">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a35e-567">Create a new request.</span></span>
  * <span data-ttu-id="4a35e-568">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4a35e-569">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4a35e-570">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4a35e-571">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="4a35e-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4a35e-572">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-572">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4a35e-574">Incorporación de un método Create</span><span class="sxs-lookup"><span data-stu-id="4a35e-574">Add a Create method</span></span>

<span data-ttu-id="4a35e-575">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a35e-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4a35e-576">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4a35e-577">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a35e-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4a35e-578">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4a35e-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4a35e-579">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4a35e-580">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4a35e-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4a35e-581">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4a35e-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4a35e-582">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="4a35e-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4a35e-583">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4a35e-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4a35e-584">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4a35e-585">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4a35e-586">Prueba del método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4a35e-587">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-587">Build the project.</span></span>
* <span data-ttu-id="4a35e-588">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4a35e-589">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="4a35e-590">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="4a35e-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4a35e-591">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="4a35e-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4a35e-592">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4a35e-593">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-593">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="4a35e-595">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4a35e-596">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="4a35e-596">Test the location header URI</span></span>

* <span data-ttu-id="4a35e-597">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4a35e-598">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="4a35e-598">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4a35e-600">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="4a35e-600">Set the method to GET.</span></span>
* <span data-ttu-id="4a35e-601">Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="4a35e-602">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4a35e-603">Incorporación de un método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="4a35e-604">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4a35e-605">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4a35e-606">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4a35e-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4a35e-607">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="4a35e-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4a35e-608">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4a35e-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4a35e-609">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4a35e-610">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="4a35e-611">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4a35e-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4a35e-612">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4a35e-613">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="4a35e-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4a35e-614">Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="4a35e-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4a35e-615">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="4a35e-615">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4a35e-617">Incorporación de un método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4a35e-618">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4a35e-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4a35e-619">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4a35e-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4a35e-620">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4a35e-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4a35e-621">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4a35e-622">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4a35e-623">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="4a35e-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="4a35e-624">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a35e-624">Select **Send**.</span></span>

<span data-ttu-id="4a35e-625">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="4a35e-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4a35e-626">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="4a35e-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4a35e-627">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a35e-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="4a35e-628">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4a35e-629">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a35e-629">jQuery initiates the request.</span></span> <span data-ttu-id="4a35e-630">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4a35e-631">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="4a35e-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4a35e-632">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4a35e-633">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4a35e-634">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="4a35e-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4a35e-635">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4a35e-636">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="4a35e-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4a35e-637">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="4a35e-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4a35e-638">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4a35e-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4a35e-639">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="4a35e-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4a35e-640">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="4a35e-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4a35e-641">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="4a35e-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4a35e-642">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="4a35e-642">Get a list of to-do items</span></span>

<span data-ttu-id="4a35e-643">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="4a35e-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4a35e-644">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4a35e-645">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="4a35e-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4a35e-646">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-646">Add a to-do item</span></span>

<span data-ttu-id="4a35e-647">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="4a35e-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4a35e-648">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="4a35e-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4a35e-649">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="4a35e-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4a35e-650">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="4a35e-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4a35e-651">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-651">Update a to-do item</span></span>

<span data-ttu-id="4a35e-652">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="4a35e-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4a35e-653">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4a35e-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4a35e-654">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="4a35e-654">Delete a to-do item</span></span>

<span data-ttu-id="4a35e-655">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="4a35e-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="4a35e-656">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="4a35e-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="4a35e-657">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4a35e-657">Additional resources</span></span>

<span data-ttu-id="4a35e-658">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="4a35e-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4a35e-659">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4a35e-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4a35e-660">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="4a35e-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4a35e-661">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="4a35e-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
