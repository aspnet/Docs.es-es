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
ms.openlocfilehash: ab06f2d347d7f05e9c72a85e0c6e0fbc67fe48a7
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550717"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="ef4e9-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef4e9-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="ef4e9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="ef4e9-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ef4e9-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef4e9-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-107">Create a web API project.</span></span>
> * <span data-ttu-id="ef4e9-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ef4e9-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ef4e9-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ef4e9-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-111">Call the web API with Postman.</span></span>

<span data-ttu-id="ef4e9-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ef4e9-113">Información general</span><span class="sxs-lookup"><span data-stu-id="ef4e9-113">Overview</span></span>

<span data-ttu-id="ef4e9-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ef4e9-115">API</span><span class="sxs-lookup"><span data-stu-id="ef4e9-115">API</span></span> | <span data-ttu-id="ef4e9-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="ef4e9-116">Description</span></span> | <span data-ttu-id="ef4e9-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="ef4e9-117">Request body</span></span> | <span data-ttu-id="ef4e9-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="ef4e9-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ef4e9-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-119">Get all to-do items</span></span> | <span data-ttu-id="ef4e9-120">Ninguna</span><span class="sxs-lookup"><span data-stu-id="ef4e9-120">None</span></span> | <span data-ttu-id="ef4e9-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ef4e9-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="ef4e9-122">Get an item by ID</span></span> | <span data-ttu-id="ef4e9-123">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-123">None</span></span> | <span data-ttu-id="ef4e9-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ef4e9-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="ef4e9-125">Add a new item</span></span> | <span data-ttu-id="ef4e9-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-126">To-do item</span></span> | <span data-ttu-id="ef4e9-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ef4e9-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ef4e9-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-129">To-do item</span></span> | <span data-ttu-id="ef4e9-130">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-130">None</span></span> |
|<span data-ttu-id="ef4e9-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-133">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-133">None</span></span> | <span data-ttu-id="ef4e9-134">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-134">None</span></span>|

<span data-ttu-id="ef4e9-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ef4e9-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ef4e9-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="ef4e9-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ef4e9-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ef4e9-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ef4e9-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 5.0** están seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="ef4e9-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef4e9-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ef4e9-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ef4e9-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ef4e9-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-158">The preceding commands:</span></span>

  * <span data-ttu-id="ef4e9-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ef4e9-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ef4e9-164">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ef4e9-165">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ef4e9-167">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 5.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="ef4e9-168">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-168">Select **Next**.</span></span>

* <span data-ttu-id="ef4e9-169">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ef4e9-171">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="ef4e9-172">Prueba del proyecto</span><span class="sxs-lookup"><span data-stu-id="ef4e9-172">Test the project</span></span>

<span data-ttu-id="ef4e9-173">La plantilla de proyecto crea una API `WeatherForecast` compatible con [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef4e9-175">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ef4e9-176">Visual Studio inicia lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-176">Visual Studio launches:</span></span>

* <span data-ttu-id="ef4e9-177">El servidor web de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-177">The IIS Express web server.</span></span>
* <span data-ttu-id="ef4e9-178">El explorador predeterminado, y navega hasta `https://localhost:<port>/https://localhost:5001/swagger/index.html`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="ef4e9-180">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ef4e9-181">En un explorador, vaya a la siguiente dirección URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-182">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef4e9-183">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ef4e9-184">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ef4e9-185">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ef4e9-186">Anexe `/swagger` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="ef4e9-187">Se abre la página de Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="ef4e9-188">Seleccione **GET** > **Try it out** > **Execute** (GET > Probar > Ejecutar).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="ef4e9-189">La página muestra lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-189">The page displays:</span></span>

* <span data-ttu-id="ef4e9-190">Comando de [Curl](https://curl.haxx.se/) para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="ef4e9-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ef4e9-191">Dirección URL para probar la API WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="ef4e9-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="ef4e9-192">Código de respuesta, cuerpo y encabezados</span><span class="sxs-lookup"><span data-stu-id="ef4e9-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="ef4e9-193">Cuadro de lista desplegable con los tipos de medios y el esquema y valor de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ef4e9-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="ef4e9-194">Swagger se usa para generar una documentación útil y páginas de ayuda para API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="ef4e9-195">Este tutorial se centra en cómo crear una API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="ef4e9-196">Para obtener más información sobre Swagger, vea <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="ef4e9-197">Copie y pegue la **dirección URL de solicitud** en el explorador: `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="ef4e9-198">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="ef4e9-199">Actualización de launchUrl</span><span class="sxs-lookup"><span data-stu-id="ef4e9-199">Update the launchUrl</span></span>

<span data-ttu-id="ef4e9-200">En *Properties\launchSettings.json*, actualice `launchUrl` de `"swagger"` a `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="ef4e9-201">Dado que Swagger se ha quitado, el marcado anterior cambia la dirección URL que se inicia con el método GET del controlador agregado en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="ef4e9-202">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="ef4e9-202">Add a model class</span></span>

<span data-ttu-id="ef4e9-203">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ef4e9-204">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-206">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ef4e9-207">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-208">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="ef4e9-209">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-210">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ef4e9-211">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-213">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ef4e9-214">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-215">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-216">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-216">Right-click the project.</span></span> <span data-ttu-id="ef4e9-217">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-218">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-218">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ef4e9-220">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ef4e9-221">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ef4e9-222">Reemplace el código de plantilla por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ef4e9-223">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ef4e9-224">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ef4e9-225">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-225">Add a database context</span></span>

<span data-ttu-id="ef4e9-226">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ef4e9-227">Esta clase se crea derivándola de la clase <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ef4e9-229">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="ef4e9-229">Add NuGet packages</span></span>

* <span data-ttu-id="ef4e9-230">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ef4e9-231">Seleccione la pestaña **Examinar** y escriba \*\*Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="ef4e9-232">**EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="ef4e9-233">Active la casilla **Incluir versión previa** para que la versión RC 5.0 esté disponible.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="ef4e9-234">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="ef4e9-235">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="ef4e9-236">Use las instrucciones anteriores para agregar el paquete NuGet **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="ef4e9-237">![Administrador de paquetes NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ef4e9-238">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="ef4e9-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="ef4e9-239">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-240">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-241">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ef4e9-242">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ef4e9-243">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ef4e9-244">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-244">Register the database context</span></span>

<span data-ttu-id="ef4e9-245">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ef4e9-246">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="ef4e9-247">Actualice *Startup.cs* con el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ef4e9-248">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-248">The preceding code:</span></span>

* <span data-ttu-id="ef4e9-249">Quita las llamadas de Swagger.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="ef4e9-250">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ef4e9-251">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ef4e9-252">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ef4e9-253">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="ef4e9-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-255">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ef4e9-256">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ef4e9-257">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ef4e9-258">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="ef4e9-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ef4e9-259">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ef4e9-260">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ef4e9-261">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-262">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ef4e9-263">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ef4e9-264">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-264">The preceding commands:</span></span>

* <span data-ttu-id="ef4e9-265">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ef4e9-266">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ef4e9-267">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ef4e9-268">El código generado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-268">The generated code:</span></span>

* <span data-ttu-id="ef4e9-269">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-270">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ef4e9-271">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ef4e9-272">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ef4e9-273">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ef4e9-274">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ef4e9-275">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ef4e9-276">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ef4e9-277">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ef4e9-278">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="ef4e9-279">Actualización del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="ef4e9-280">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="ef4e9-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ef4e9-281">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-282">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ef4e9-283">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ef4e9-284">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ef4e9-285">Devuelve un [código de estado HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="ef4e9-286">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ef4e9-287">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ef4e9-288">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ef4e9-289">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ef4e9-290">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ef4e9-291">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ef4e9-292">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-292">Install Postman</span></span>

<span data-ttu-id="ef4e9-293">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ef4e9-294">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ef4e9-295">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-295">Start the web app.</span></span>
* <span data-ttu-id="ef4e9-296">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-296">Start Postman.</span></span>
* <span data-ttu-id="ef4e9-297">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ef4e9-298">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ef4e9-299">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ef4e9-300">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ef4e9-301">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-301">Create a new request.</span></span>
* <span data-ttu-id="ef4e9-302">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ef4e9-303">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ef4e9-304">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ef4e9-305">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="ef4e9-306">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ef4e9-307">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ef4e9-308">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ef4e9-309">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-309">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="ef4e9-311">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="ef4e9-311">Test the location header URI</span></span>

<span data-ttu-id="ef4e9-312">El URI del encabezado de ubicación se puede probar en el explorador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="ef4e9-313">Copie y pegue el URI del encabezado de ubicación en el explorador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="ef4e9-314">Para probarlo en Postman:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-314">To test in Postman:</span></span>

* <span data-ttu-id="ef4e9-315">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ef4e9-316">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-316">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="ef4e9-318">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ef4e9-319">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ef4e9-320">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ef4e9-321">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ef4e9-322">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="ef4e9-322">Examine the GET methods</span></span>

<span data-ttu-id="ef4e9-323">Se implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ef4e9-324">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ef4e9-325">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ef4e9-326">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ef4e9-327">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-327">Test Get with Postman</span></span>

* <span data-ttu-id="ef4e9-328">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-328">Create a new request.</span></span>
* <span data-ttu-id="ef4e9-329">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ef4e9-330">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ef4e9-331">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ef4e9-332">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ef4e9-333">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-333">Select **Send**.</span></span>

<span data-ttu-id="ef4e9-334">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-334">This app uses an in-memory database.</span></span> <span data-ttu-id="ef4e9-335">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ef4e9-336">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ef4e9-337">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="ef4e9-337">Routing and URL paths</span></span>

<span data-ttu-id="ef4e9-338">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ef4e9-339">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ef4e9-340">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ef4e9-341">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ef4e9-342">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ef4e9-343">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ef4e9-344">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ef4e9-345">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-345">This sample doesn't use a template.</span></span> <span data-ttu-id="ef4e9-346">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ef4e9-347">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ef4e9-348">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ef4e9-349">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-349">Return values</span></span>

<span data-ttu-id="ef4e9-350">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ef4e9-351">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ef4e9-352">El código de respuesta de este tipo de valor devuelto es [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ef4e9-353">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ef4e9-354">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ef4e9-355">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ef4e9-356">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error de [estado 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ef4e9-357">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ef4e9-358">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ef4e9-359">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-359">The PutTodoItem method</span></span>

<span data-ttu-id="ef4e9-360">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ef4e9-361">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ef4e9-362">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ef4e9-363">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ef4e9-364">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ef4e9-365">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ef4e9-366">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="ef4e9-367">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ef4e9-368">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ef4e9-369">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ef4e9-370">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ef4e9-371">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-371">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ef4e9-373">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="ef4e9-374">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ef4e9-375">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ef4e9-376">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ef4e9-377">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ef4e9-378">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ef4e9-379">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-379">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ef4e9-380">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="ef4e9-380">Prevent over-posting</span></span>

<span data-ttu-id="ef4e9-381">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ef4e9-382">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ef4e9-383">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ef4e9-384">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ef4e9-385">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="ef4e9-386">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-386">A DTO may be used to:</span></span>

* <span data-ttu-id="ef4e9-387">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-387">Prevent over-posting.</span></span>
* <span data-ttu-id="ef4e9-388">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ef4e9-389">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ef4e9-390">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ef4e9-391">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ef4e9-392">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="ef4e9-393">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ef4e9-394">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ef4e9-395">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ef4e9-396">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ef4e9-397">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ef4e9-398">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="ef4e9-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="ef4e9-399">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ef4e9-400">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef4e9-401">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-401">Create a web API project.</span></span>
> * <span data-ttu-id="ef4e9-402">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ef4e9-403">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ef4e9-404">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ef4e9-405">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-405">Call the web API with Postman.</span></span>

<span data-ttu-id="ef4e9-406">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ef4e9-407">Información general</span><span class="sxs-lookup"><span data-stu-id="ef4e9-407">Overview</span></span>

<span data-ttu-id="ef4e9-408">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ef4e9-409">API</span><span class="sxs-lookup"><span data-stu-id="ef4e9-409">API</span></span> | <span data-ttu-id="ef4e9-410">Descripción</span><span class="sxs-lookup"><span data-stu-id="ef4e9-410">Description</span></span> | <span data-ttu-id="ef4e9-411">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="ef4e9-411">Request body</span></span> | <span data-ttu-id="ef4e9-412">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="ef4e9-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="ef4e9-413">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-413">Get all to-do items</span></span> | <span data-ttu-id="ef4e9-414">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-414">None</span></span> | <span data-ttu-id="ef4e9-415">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="ef4e9-416">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="ef4e9-416">Get an item by ID</span></span> | <span data-ttu-id="ef4e9-417">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-417">None</span></span> | <span data-ttu-id="ef4e9-418">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="ef4e9-419">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="ef4e9-419">Add a new item</span></span> | <span data-ttu-id="ef4e9-420">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-420">To-do item</span></span> | <span data-ttu-id="ef4e9-421">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="ef4e9-422">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ef4e9-423">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-423">To-do item</span></span> | <span data-ttu-id="ef4e9-424">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-424">None</span></span> |
|<span data-ttu-id="ef4e9-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-426">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-427">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-427">None</span></span> | <span data-ttu-id="ef4e9-428">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-428">None</span></span>|

<span data-ttu-id="ef4e9-429">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-429">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ef4e9-435">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ef4e9-439">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="ef4e9-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-441">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ef4e9-442">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ef4e9-443">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ef4e9-444">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="ef4e9-445">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-445">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-448">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef4e9-449">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ef4e9-450">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ef4e9-451">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ef4e9-452">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-452">The preceding commands:</span></span>

  * <span data-ttu-id="ef4e9-453">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ef4e9-454">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-455">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-456">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-456">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ef4e9-458">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ef4e9-459">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Selección de plantilla de API de macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="ef4e9-461">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 3.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="ef4e9-462">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-462">Select **Next**.</span></span>

* <span data-ttu-id="ef4e9-463">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ef4e9-465">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="ef4e9-466">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="ef4e9-466">Test the API</span></span>

<span data-ttu-id="ef4e9-467">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="ef4e9-468">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef4e9-470">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ef4e9-471">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ef4e9-472">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ef4e9-473">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef4e9-475">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ef4e9-476">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-477">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef4e9-478">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ef4e9-479">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ef4e9-480">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ef4e9-481">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="ef4e9-482">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="ef4e9-483">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="ef4e9-483">Add a model class</span></span>

<span data-ttu-id="ef4e9-484">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ef4e9-485">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-487">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ef4e9-488">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-489">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="ef4e9-490">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-491">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ef4e9-492">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-494">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ef4e9-495">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-496">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-497">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-497">Right-click the project.</span></span> <span data-ttu-id="ef4e9-498">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-499">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-499">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ef4e9-501">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ef4e9-502">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ef4e9-503">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="ef4e9-504">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ef4e9-505">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ef4e9-506">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-506">Add a database context</span></span>

<span data-ttu-id="ef4e9-507">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ef4e9-508">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="ef4e9-510">Adición de paquetes NuGet</span><span class="sxs-lookup"><span data-stu-id="ef4e9-510">Add NuGet packages</span></span>

* <span data-ttu-id="ef4e9-511">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ef4e9-512">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="ef4e9-513">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="ef4e9-514">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="ef4e9-515">Use las instrucciones anteriores para agregar el paquete NuGet **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ef4e9-517">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="ef4e9-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="ef4e9-518">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-519">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-520">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ef4e9-521">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ef4e9-522">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ef4e9-523">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-523">Register the database context</span></span>

<span data-ttu-id="ef4e9-524">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ef4e9-525">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="ef4e9-526">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ef4e9-527">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-527">The preceding code:</span></span>

* <span data-ttu-id="ef4e9-528">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ef4e9-529">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ef4e9-530">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ef4e9-531">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="ef4e9-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-533">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ef4e9-534">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ef4e9-535">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ef4e9-536">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="ef4e9-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ef4e9-537">Seleccione **TodoItem (TodoApi.Models)** en la **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ef4e9-538">Seleccione **TodoContext (TodoApi.Models)** en la **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ef4e9-539">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-540">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ef4e9-541">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ef4e9-542">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-542">The preceding commands:</span></span>

* <span data-ttu-id="ef4e9-543">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ef4e9-544">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ef4e9-545">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ef4e9-546">El código generado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-546">The generated code:</span></span>

* <span data-ttu-id="ef4e9-547">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-548">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ef4e9-549">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ef4e9-550">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ef4e9-551">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="ef4e9-552">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="ef4e9-553">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="ef4e9-554">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="ef4e9-555">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="ef4e9-556">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="ef4e9-557">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="ef4e9-558">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="ef4e9-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ef4e9-559">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-560">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ef4e9-561">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ef4e9-562">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ef4e9-563">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="ef4e9-564">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ef4e9-565">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ef4e9-566">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ef4e9-567">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ef4e9-568">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ef4e9-569">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ef4e9-570">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-570">Install Postman</span></span>

<span data-ttu-id="ef4e9-571">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ef4e9-572">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ef4e9-573">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-573">Start the web app.</span></span>
* <span data-ttu-id="ef4e9-574">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-574">Start Postman.</span></span>
* <span data-ttu-id="ef4e9-575">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="ef4e9-576">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ef4e9-577">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ef4e9-578">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ef4e9-579">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-579">Create a new request.</span></span>
* <span data-ttu-id="ef4e9-580">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ef4e9-581">Establezca el URI en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ef4e9-582">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ef4e9-583">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="ef4e9-584">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ef4e9-585">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ef4e9-586">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ef4e9-587">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-587">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="ef4e9-589">Prueba del URI del encabezado de ubicación con Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="ef4e9-590">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ef4e9-591">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-591">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="ef4e9-593">Establezca el método HTTP en `GET`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="ef4e9-594">Establezca el URI en `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="ef4e9-595">Por ejemplo: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="ef4e9-596">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ef4e9-597">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="ef4e9-597">Examine the GET methods</span></span>

<span data-ttu-id="ef4e9-598">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ef4e9-599">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ef4e9-600">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="ef4e9-601">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ef4e9-602">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="ef4e9-602">Test Get with Postman</span></span>

* <span data-ttu-id="ef4e9-603">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-603">Create a new request.</span></span>
* <span data-ttu-id="ef4e9-604">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ef4e9-605">Establezca el URI de solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ef4e9-606">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ef4e9-607">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ef4e9-608">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-608">Select **Send**.</span></span>

<span data-ttu-id="ef4e9-609">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-609">This app uses an in-memory database.</span></span> <span data-ttu-id="ef4e9-610">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ef4e9-611">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ef4e9-612">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="ef4e9-612">Routing and URL paths</span></span>

<span data-ttu-id="ef4e9-613">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ef4e9-614">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ef4e9-615">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ef4e9-616">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ef4e9-617">En este ejemplo, el nombre de clase de controlador es **TodoItems** Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ef4e9-618">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ef4e9-619">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ef4e9-620">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-620">This sample doesn't use a template.</span></span> <span data-ttu-id="ef4e9-621">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ef4e9-622">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ef4e9-623">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ef4e9-624">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="ef4e9-624">Return values</span></span> 

<span data-ttu-id="ef4e9-625">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ef4e9-626">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ef4e9-627">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ef4e9-628">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ef4e9-629">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ef4e9-630">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ef4e9-631">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ef4e9-632">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ef4e9-633">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ef4e9-634">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-634">The PutTodoItem method</span></span>

<span data-ttu-id="ef4e9-635">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ef4e9-636">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ef4e9-637">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ef4e9-638">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ef4e9-639">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ef4e9-640">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ef4e9-641">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="ef4e9-642">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ef4e9-643">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ef4e9-644">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ef4e9-645">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="ef4e9-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ef4e9-646">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-646">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ef4e9-648">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="ef4e9-649">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ef4e9-650">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="ef4e9-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ef4e9-651">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ef4e9-652">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ef4e9-653">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ef4e9-654">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-654">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="ef4e9-655">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="ef4e9-655">Prevent over-posting</span></span>

<span data-ttu-id="ef4e9-656">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="ef4e9-657">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="ef4e9-658">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="ef4e9-659">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="ef4e9-660">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="ef4e9-661">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-661">A DTO may be used to:</span></span>

* <span data-ttu-id="ef4e9-662">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-662">Prevent over-posting.</span></span>
* <span data-ttu-id="ef4e9-663">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="ef4e9-664">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="ef4e9-665">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="ef4e9-666">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="ef4e9-667">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="ef4e9-668">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="ef4e9-669">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="ef4e9-670">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="ef4e9-671">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="ef4e9-672">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ef4e9-673">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="ef4e9-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="ef4e9-674">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ef4e9-675">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef4e9-676">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-676">Create a web API project.</span></span>
> * <span data-ttu-id="ef4e9-677">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ef4e9-678">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-678">Add a controller.</span></span>
> * <span data-ttu-id="ef4e9-679">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="ef4e9-680">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="ef4e9-681">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-681">Specify return values.</span></span>
> * <span data-ttu-id="ef4e9-682">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="ef4e9-683">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="ef4e9-684">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="ef4e9-685">Información general (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-685">Overview 2.1</span></span>

<span data-ttu-id="ef4e9-686">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ef4e9-687">API</span><span class="sxs-lookup"><span data-stu-id="ef4e9-687">API</span></span> | <span data-ttu-id="ef4e9-688">Descripción</span><span class="sxs-lookup"><span data-stu-id="ef4e9-688">Description</span></span> | <span data-ttu-id="ef4e9-689">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="ef4e9-689">Request body</span></span> | <span data-ttu-id="ef4e9-690">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="ef4e9-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="ef4e9-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ef4e9-691">GET /api/TodoItems</span></span> | <span data-ttu-id="ef4e9-692">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-692">Get all to-do items</span></span> | <span data-ttu-id="ef4e9-693">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-693">None</span></span> | <span data-ttu-id="ef4e9-694">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="ef4e9-694">Array of to-do items</span></span>|
|<span data-ttu-id="ef4e9-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ef4e9-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="ef4e9-696">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="ef4e9-696">Get an item by ID</span></span> | <span data-ttu-id="ef4e9-697">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-697">None</span></span> | <span data-ttu-id="ef4e9-698">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-698">To-do item</span></span>|
|<span data-ttu-id="ef4e9-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ef4e9-699">POST /api/TodoItems</span></span> | <span data-ttu-id="ef4e9-700">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="ef4e9-700">Add a new item</span></span> | <span data-ttu-id="ef4e9-701">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-701">To-do item</span></span> | <span data-ttu-id="ef4e9-702">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-702">To-do item</span></span> |
|<span data-ttu-id="ef4e9-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ef4e9-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="ef4e9-704">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ef4e9-705">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="ef4e9-705">To-do item</span></span> | <span data-ttu-id="ef4e9-706">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-706">None</span></span> |
|<span data-ttu-id="ef4e9-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-708">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ef4e9-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ef4e9-709">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-709">None</span></span> | <span data-ttu-id="ef4e9-710">None</span><span class="sxs-lookup"><span data-stu-id="ef4e9-710">None</span></span>|

<span data-ttu-id="ef4e9-711">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-711">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="ef4e9-717">Requisitos previos (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-720">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="ef4e9-721">Creación de un proyecto web (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-723">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ef4e9-724">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ef4e9-725">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ef4e9-726">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="ef4e9-727">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="ef4e9-728">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-728">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-731">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef4e9-732">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ef4e9-733">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="ef4e9-734">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="ef4e9-735">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-736">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-737">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-737">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ef4e9-739">En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="ef4e9-740">En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="ef4e9-741">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, seleccione la **plataforma de destino** .NET Core 2.x más reciente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="ef4e9-742">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-742">Select **Next**.</span></span>

* <span data-ttu-id="ef4e9-743">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="ef4e9-745">Prueba de la API (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-745">Test the API 2.1</span></span>

<span data-ttu-id="ef4e9-746">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-746">The project template creates a `values` API.</span></span> <span data-ttu-id="ef4e9-747">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef4e9-749">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ef4e9-750">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ef4e9-751">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ef4e9-752">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef4e9-754">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ef4e9-755">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-756">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef4e9-757">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ef4e9-758">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ef4e9-759">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ef4e9-760">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="ef4e9-761">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="ef4e9-762">Incorporación de una clase de modelo (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-762">Add a model class 2.1</span></span>

<span data-ttu-id="ef4e9-763">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ef4e9-764">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-766">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ef4e9-767">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-768">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="ef4e9-769">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-770">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ef4e9-771">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef4e9-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef4e9-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ef4e9-773">Agregue una carpeta denominada *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ef4e9-774">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-775">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef4e9-776">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-776">Right-click the project.</span></span> <span data-ttu-id="ef4e9-777">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ef4e9-778">Asigne a la carpeta el nombre *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-778">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ef4e9-780">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ef4e9-781">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ef4e9-782">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="ef4e9-783">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ef4e9-784">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero, por convención, se usa la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="ef4e9-785">Incorporación de un contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-785">Add a database context 2.1</span></span>

<span data-ttu-id="ef4e9-786">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ef4e9-787">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-789">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ef4e9-790">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-791">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ef4e9-792">Agregue una clase `TodoContext` a la carpeta *Models* .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ef4e9-793">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="ef4e9-794">Registro del contexto de base de datos (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-794">Register the database context 2.1</span></span>

<span data-ttu-id="ef4e9-795">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ef4e9-796">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="ef4e9-797">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="ef4e9-798">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-798">The preceding code:</span></span>

* <span data-ttu-id="ef4e9-799">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ef4e9-800">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ef4e9-801">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="ef4e9-802">Incorporación de un controlador (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-804">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ef4e9-805">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="ef4e9-806">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="ef4e9-807">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-809">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ef4e9-810">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="ef4e9-811">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="ef4e9-812">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-812">The preceding code:</span></span>

* <span data-ttu-id="ef4e9-813">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="ef4e9-814">Marca la clase con el atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-815">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ef4e9-816">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ef4e9-817">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ef4e9-818">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="ef4e9-819">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="ef4e9-820">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="ef4e9-821">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="ef4e9-822">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="ef4e9-823">Incorporación de métodos Get (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-823">Add Get methods 2.1</span></span>

<span data-ttu-id="ef4e9-824">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="ef4e9-825">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="ef4e9-826">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-826">Stop the app if it's still running.</span></span> <span data-ttu-id="ef4e9-827">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="ef4e9-828">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="ef4e9-829">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="ef4e9-830">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="ef4e9-831">Enrutamiento y rutas URL (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="ef4e9-832">El atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ef4e9-833">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ef4e9-834">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="ef4e9-835">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ef4e9-836">En este ejemplo, el nombre de clase de controlador es **Todo** Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="ef4e9-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="ef4e9-837">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ef4e9-838">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ef4e9-839">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-839">This sample doesn't use a template.</span></span> <span data-ttu-id="ef4e9-840">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ef4e9-841">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ef4e9-842">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="ef4e9-843">Valores devueltos (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-843">Return values 2.1</span></span>

<span data-ttu-id="ef4e9-844">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ef4e9-845">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ef4e9-846">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ef4e9-847">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ef4e9-848">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ef4e9-849">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ef4e9-850">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="ef4e9-851">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ef4e9-852">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="ef4e9-853">Prueba del método GetTodoItems (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="ef4e9-854">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ef4e9-855">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="ef4e9-856">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-856">Start the web app.</span></span>
* <span data-ttu-id="ef4e9-857">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-857">Start Postman.</span></span>
* <span data-ttu-id="ef4e9-858">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef4e9-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef4e9-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef4e9-860">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ef4e9-861">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="ef4e9-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ef4e9-862">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="ef4e9-863">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="ef4e9-864">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="ef4e9-865">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-865">Create a new request.</span></span>
  * <span data-ttu-id="ef4e9-866">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="ef4e9-867">Establezca el URI de solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="ef4e9-868">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="ef4e9-869">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ef4e9-870">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-870">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="ef4e9-872">Incorporación de un método Create (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-872">Add a Create method 2.1</span></span>

<span data-ttu-id="ef4e9-873">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="ef4e9-874">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="ef4e9-875">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ef4e9-876">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="ef4e9-877">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="ef4e9-878">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ef4e9-879">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="ef4e9-880">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="ef4e9-881">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ef4e9-882">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ef4e9-883">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="ef4e9-884">Prueba del método PostTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="ef4e9-885">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-885">Build the project.</span></span>
* <span data-ttu-id="ef4e9-886">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ef4e9-887">Establezca el URI en `https://localhost:<port>/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="ef4e9-888">Por ejemplo: `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="ef4e9-889">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="ef4e9-890">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ef4e9-891">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="ef4e9-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ef4e9-892">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ef4e9-893">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-893">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="ef4e9-895">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="ef4e9-896">Prueba del URI del encabezado de ubicación (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="ef4e9-897">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ef4e9-898">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-898">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="ef4e9-900">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-900">Set the method to GET.</span></span>
* <span data-ttu-id="ef4e9-901">Establezca el URI en `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="ef4e9-902">Por ejemplo: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="ef4e9-903">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="ef4e9-904">Incorporación de un método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="ef4e9-905">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="ef4e9-906">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ef4e9-907">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ef4e9-908">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ef4e9-909">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ef4e9-910">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="ef4e9-911">Prueba del método PutTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="ef4e9-912">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ef4e9-913">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ef4e9-914">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ef4e9-915">Actualice el elemento to-do que tiene el Id = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="ef4e9-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ef4e9-916">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-916">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="ef4e9-918">Incorporación de un método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ef4e9-919">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="ef4e9-920">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="ef4e9-921">Prueba del método DeleteTodoItem (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="ef4e9-922">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ef4e9-923">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ef4e9-924">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="ef4e9-925">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-925">Select **Send**.</span></span>

<span data-ttu-id="ef4e9-926">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="ef4e9-927">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="ef4e9-928">Llamada a la API web con JavaScript (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="ef4e9-929">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="ef4e9-930">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-930">jQuery initiates the request.</span></span> <span data-ttu-id="ef4e9-931">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="ef4e9-932">Configure la aplicación para [atender archivos estáticos](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) y [habilitar la asignación de archivos predeterminada](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="ef4e9-933">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="ef4e9-934">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="ef4e9-935">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="ef4e9-936">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="ef4e9-937">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="ef4e9-938">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="ef4e9-939">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="ef4e9-940">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="ef4e9-941">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="ef4e9-942">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="ef4e9-943">Obtención de una lista de tareas pendientes (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="ef4e9-944">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="ef4e9-945">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="ef4e9-946">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="ef4e9-947">Incorporación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="ef4e9-948">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="ef4e9-949">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="ef4e9-950">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="ef4e9-951">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="ef4e9-952">Actualización de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="ef4e9-953">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="ef4e9-954">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="ef4e9-955">Eliminación de una tarea pendiente (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="ef4e9-956">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="ef4e9-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="ef4e9-957">Incorporación de compatibilidad de autenticación a una API web (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="ef4e9-958">Recursos adicionales (2.1)</span><span class="sxs-lookup"><span data-stu-id="ef4e9-958">Additional resources 2.1</span></span>

<span data-ttu-id="ef4e9-959">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="ef4e9-960">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ef4e9-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ef4e9-961">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="ef4e9-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="ef4e9-962">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="ef4e9-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
