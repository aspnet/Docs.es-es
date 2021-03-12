---
title: Migrar de ASP.NET Web API a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar una implementación de Web API desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 3bd34f677271ddfc00e6e65ddf3a5e3c10eec863
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588788"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="95d4b-103">Migrar de ASP.NET Web API a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95d4b-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="95d4b-104">Por [Scott Addie](https://twitter.com/scott_addie) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="95d4b-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="95d4b-105">Una API Web ASP.NET 4. x es un servicio HTTP que llega a una amplia gama de clientes, incluidos exploradores y dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="95d4b-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="95d4b-106">ASP.NET Core combina modelos de aplicación de MVC y Web API de ASP.NET 4. x en un modelo de programación único conocido como ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="95d4b-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="95d4b-107">En este artículo se muestran los pasos necesarios para migrar desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.</span><span class="sxs-lookup"><span data-stu-id="95d4b-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="95d4b-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="95d4b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="95d4b-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="95d4b-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="95d4b-110">Revisar el proyecto de Web API de ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="95d4b-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="95d4b-111">En este artículo se usa el proyecto *ProductsApp* creado en [Introducción con ASP.net web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="95d4b-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="95d4b-112">En ese proyecto, se configura un proyecto de API Web ASP.NET 4. x básico como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="95d4b-113">En *global.asax.CS*, se realiza una llamada a `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="95d4b-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="95d4b-114">La `WebApiConfig` clase se encuentra en la carpeta *App_Start* y tiene un `Register` método estático:</span><span class="sxs-lookup"><span data-stu-id="95d4b-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="95d4b-115">La clase anterior:</span><span class="sxs-lookup"><span data-stu-id="95d4b-115">The preceding class:</span></span>

* <span data-ttu-id="95d4b-116">Configura el [enrutamiento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), aunque no se usa realmente.</span><span class="sxs-lookup"><span data-stu-id="95d4b-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="95d4b-117">Configura la tabla de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="95d4b-117">Configures the routing table.</span></span>
<span data-ttu-id="95d4b-118">El código de ejemplo espera que las direcciones URL coincidan con el formato `/api/{controller}/{id}` , `{id}` siendo opcionales.</span><span class="sxs-lookup"><span data-stu-id="95d4b-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="95d4b-119">En las secciones siguientes se muestra cómo migrar el proyecto de Web API a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="95d4b-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="95d4b-120">Crear el proyecto de destino</span><span class="sxs-lookup"><span data-stu-id="95d4b-120">Create the destination project</span></span>

<span data-ttu-id="95d4b-121">Cree una nueva solución en blanco en Visual Studio y agregue el proyecto de API Web ASP.NET 4. x para migrar:</span><span class="sxs-lookup"><span data-stu-id="95d4b-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="95d4b-122">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="95d4b-123">Seleccione la plantilla **solución en blanco** y seleccione **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="95d4b-124">Asigne a la solución el nombre *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="95d4b-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="95d4b-125">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-125">Select **Create**.</span></span>
1. <span data-ttu-id="95d4b-126">Agregue el proyecto *ProductsApp* existente a la solución.</span><span class="sxs-lookup"><span data-stu-id="95d4b-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="95d4b-127">Agregue un nuevo proyecto de API para migrar a:</span><span class="sxs-lookup"><span data-stu-id="95d4b-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="95d4b-128">Agregue un nuevo proyecto de **aplicación Web de ASP.net Core** a la solución.</span><span class="sxs-lookup"><span data-stu-id="95d4b-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="95d4b-129">En el cuadro de diálogo **configurar el nuevo proyecto** , asigne al proyecto el nombre *ProductsCore* y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="95d4b-130">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="95d4b-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="95d4b-131">Seleccione la plantilla de proyecto **API** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="95d4b-132">Quite los archivos de ejemplo *WeatherForecast.CS* y *Controllers/WeatherForecastController. CS* del nuevo proyecto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="95d4b-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="95d4b-133">La solución ahora contiene dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="95d4b-133">The solution now contains two projects.</span></span> <span data-ttu-id="95d4b-134">En las secciones siguientes se explica cómo migrar el contenido del proyecto *ProductsApp* al proyecto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="95d4b-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="95d4b-135">Migración de la configuración</span><span class="sxs-lookup"><span data-stu-id="95d4b-135">Migrate configuration</span></span>

<span data-ttu-id="95d4b-136">ASP.NET Core no utiliza la carpeta *App_Start* o el archivo *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="95d4b-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="95d4b-137">Además, el archivo de *web.config* se agrega en el momento de la publicación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="95d4b-138">La clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="95d4b-138">The `Startup` class:</span></span>

* <span data-ttu-id="95d4b-139">Reemplaza a *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="95d4b-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="95d4b-140">Controla todas las tareas de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="95d4b-141">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="95d4b-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="95d4b-142">Migración de modelos y controladores</span><span class="sxs-lookup"><span data-stu-id="95d4b-142">Migrate models and controllers</span></span>

<span data-ttu-id="95d4b-143">En el código siguiente se muestra el `ProductsController` que se va a actualizar para ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="95d4b-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="95d4b-144">Actualice el `ProductsController` para ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="95d4b-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="95d4b-145">Copie *Controllers/ProductsController. CS* y la carpeta *Models* del proyecto original al nuevo.</span><span class="sxs-lookup"><span data-stu-id="95d4b-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="95d4b-146">Cambie el espacio de nombres de la raíz de los archivos copiados a `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="95d4b-147">Actualice la `using ProductsApp.Models;` instrucción a `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="95d4b-148">Los componentes siguientes no existen en ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="95d4b-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="95d4b-149">Clase `ApiController`</span><span class="sxs-lookup"><span data-stu-id="95d4b-149">`ApiController` class</span></span>
* <span data-ttu-id="95d4b-150">Espacio de nombres `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="95d4b-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="95d4b-151">`IHttpActionResult` (interfaz)</span><span class="sxs-lookup"><span data-stu-id="95d4b-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="95d4b-152">Realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="95d4b-152">Make the following changes:</span></span>

1. <span data-ttu-id="95d4b-153">Cambio de `ApiController` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="95d4b-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="95d4b-154">Agregue `using Microsoft.AspNetCore.Mvc;` para resolver la `ControllerBase` referencia.</span><span class="sxs-lookup"><span data-stu-id="95d4b-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="95d4b-155">Elimine `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="95d4b-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="95d4b-156">Cambie el `GetProduct` tipo de valor devuelto de la acción de `IHttpActionResult` a `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="95d4b-157">Simplifique la `GetProduct` instrucción de la acción `return` para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="95d4b-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="95d4b-158">Configuración del enrutamiento</span><span class="sxs-lookup"><span data-stu-id="95d4b-158">Configure routing</span></span>

<span data-ttu-id="95d4b-159">La plantilla de proyecto de *API* de ASP.net Core incluye la configuración de enrutamiento de punto de conexión en el código generado.</span><span class="sxs-lookup"><span data-stu-id="95d4b-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="95d4b-160">Lo siguiente <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> y <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> llama a:</span><span class="sxs-lookup"><span data-stu-id="95d4b-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="95d4b-161">Registra la coincidencia de rutas y la ejecución de puntos de conexión en la canalización de [middleware](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="95d4b-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="95d4b-162">Reemplace el archivo *App_Start/webapiconfig.CS* del proyecto *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="95d4b-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="95d4b-163">Configure el enrutamiento de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="95d4b-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="95d4b-164">Marque la `ProductsController` clase con los siguientes atributos:</span><span class="sxs-lookup"><span data-stu-id="95d4b-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="95d4b-165">El [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura el patrón de enrutamiento de atributo del controlador.</span><span class="sxs-lookup"><span data-stu-id="95d4b-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="95d4b-166">El [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo hace que el enrutamiento de atributos sea un requisito para todas las acciones de este controlador.</span><span class="sxs-lookup"><span data-stu-id="95d4b-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="95d4b-167">El enrutamiento de atributos admite tokens, como `[controller]` y `[action]` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="95d4b-168">En tiempo de ejecución, cada token se reemplaza por el nombre del controlador o la acción, respectivamente, a la que se ha aplicado el atributo.</span><span class="sxs-lookup"><span data-stu-id="95d4b-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="95d4b-169">Los tokens:</span><span class="sxs-lookup"><span data-stu-id="95d4b-169">The tokens:</span></span>
    * <span data-ttu-id="95d4b-170">Reduzca el número de cadenas mágicas en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="95d4b-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="95d4b-171">Asegúrese de que las rutas permanecen sincronizadas con las acciones y controladores correspondientes cuando se aplican las refactorizaciones de cambio de nombre automáticas.</span><span class="sxs-lookup"><span data-stu-id="95d4b-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="95d4b-172">Habilite las solicitudes HTTP GET para las `ProductController` acciones:</span><span class="sxs-lookup"><span data-stu-id="95d4b-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="95d4b-173">Aplique el [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo a la `GetAllProducts` acción.</span><span class="sxs-lookup"><span data-stu-id="95d4b-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="95d4b-174">Aplique el `[HttpGet("{id}")]` atributo a la `GetProduct` acción.</span><span class="sxs-lookup"><span data-stu-id="95d4b-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="95d4b-175">Ejecute el proyecto migrado y vaya a `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="95d4b-176">Aparece una lista completa de tres productos.</span><span class="sxs-lookup"><span data-stu-id="95d4b-176">A full list of three products appears.</span></span> <span data-ttu-id="95d4b-177">Vaya a `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="95d4b-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="95d4b-178">Aparece el primer producto.</span><span class="sxs-lookup"><span data-stu-id="95d4b-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95d4b-179">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="95d4b-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="95d4b-180">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="95d4b-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="95d4b-181">Revisar el proyecto de Web API de ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="95d4b-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="95d4b-182">En este artículo se usa el proyecto *ProductsApp* creado en [Introducción con ASP.net web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="95d4b-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="95d4b-183">En ese proyecto, se configura un proyecto de API Web ASP.NET 4. x básico como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="95d4b-184">En *global.asax.CS*, se realiza una llamada a `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="95d4b-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="95d4b-185">La `WebApiConfig` clase se encuentra en la carpeta *App_Start* y tiene un `Register` método estático:</span><span class="sxs-lookup"><span data-stu-id="95d4b-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="95d4b-186">Esta clase configura el [enrutamiento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), aunque en realidad no se usa en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="95d4b-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="95d4b-187">También configura la tabla de enrutamiento, que se usa en ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="95d4b-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="95d4b-188">En este caso, la API Web de ASP.NET 4. x espera que las direcciones URL coincidan con el formato `/api/{controller}/{id}` , `{id}` siendo opcionales.</span><span class="sxs-lookup"><span data-stu-id="95d4b-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="95d4b-189">En las secciones siguientes se muestra cómo migrar el proyecto de Web API a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="95d4b-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="95d4b-190">Crear el proyecto de destino</span><span class="sxs-lookup"><span data-stu-id="95d4b-190">Create the destination project</span></span>

<span data-ttu-id="95d4b-191">Siga los pasos que se describen a continuación en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="95d4b-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="95d4b-192">Vaya a **archivo**  >  **nuevo**  >  **proyecto**  >  **otros tipos de proyecto**  >  **soluciones de Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="95d4b-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="95d4b-193">Seleccione **solución en blanco** y asigne el nombre *WebAPIMigration* a la solución.</span><span class="sxs-lookup"><span data-stu-id="95d4b-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="95d4b-194">Haga clic en el botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="95d4b-194">Click the **OK** button.</span></span>
* <span data-ttu-id="95d4b-195">Agregue el proyecto *ProductsApp* existente a la solución.</span><span class="sxs-lookup"><span data-stu-id="95d4b-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="95d4b-196">Agregue un nuevo proyecto de **aplicación Web de ASP.net Core** a la solución.</span><span class="sxs-lookup"><span data-stu-id="95d4b-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="95d4b-197">Seleccione la plataforma de destino de **.net Core** en la lista desplegable y seleccione la plantilla de proyecto de **API** .</span><span class="sxs-lookup"><span data-stu-id="95d4b-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="95d4b-198">Asigne al proyecto el nombre *ProductsCore* y haga clic en el botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="95d4b-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="95d4b-199">La solución ahora contiene dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="95d4b-199">The solution now contains two projects.</span></span> <span data-ttu-id="95d4b-200">En las secciones siguientes se explica cómo migrar el contenido del proyecto *ProductsApp* al proyecto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="95d4b-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="95d4b-201">Migración de la configuración</span><span class="sxs-lookup"><span data-stu-id="95d4b-201">Migrate configuration</span></span>

<span data-ttu-id="95d4b-202">ASP.NET Core no usa:</span><span class="sxs-lookup"><span data-stu-id="95d4b-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="95d4b-203">*App_Start* carpeta o el archivo *global. asax*</span><span class="sxs-lookup"><span data-stu-id="95d4b-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="95d4b-204">*web.config* archivo se agrega en el momento de la publicación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="95d4b-205">La clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="95d4b-205">The `Startup` class:</span></span>

* <span data-ttu-id="95d4b-206">Reemplaza a *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="95d4b-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="95d4b-207">Controla todas las tareas de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="95d4b-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="95d4b-208">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="95d4b-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="95d4b-209">En ASP.NET Core MVC, el enrutamiento de atributos se incluye de forma predeterminada cuando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> se llama a en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="95d4b-210">La siguiente `UseMvc` llamada reemplaza el archivo *App_Start/webapiconfig.CS* del proyecto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="95d4b-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="95d4b-211">Migración de modelos y controladores</span><span class="sxs-lookup"><span data-stu-id="95d4b-211">Migrate models and controllers</span></span>

<span data-ttu-id="95d4b-212">En el código siguiente se muestra la `ProductsController` actualización para ASP.net Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="95d4b-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="95d4b-213">Actualice el `ProductsController` para ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="95d4b-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="95d4b-214">Copie *Controllers/ProductsController. CS* del proyecto original al nuevo.</span><span class="sxs-lookup"><span data-stu-id="95d4b-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="95d4b-215">Copie la carpeta *Models* del proyecto original al nuevo.</span><span class="sxs-lookup"><span data-stu-id="95d4b-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="95d4b-216">Cambie el espacio de nombres de la raíz de los archivos copiados a `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="95d4b-217">Actualice la `using ProductsApp.Models;` instrucción a `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="95d4b-218">Los componentes siguientes no existen en ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="95d4b-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="95d4b-219">Clase `ApiController`</span><span class="sxs-lookup"><span data-stu-id="95d4b-219">`ApiController` class</span></span>
* <span data-ttu-id="95d4b-220">Espacio de nombres `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="95d4b-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="95d4b-221">`IHttpActionResult` (interfaz)</span><span class="sxs-lookup"><span data-stu-id="95d4b-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="95d4b-222">Realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="95d4b-222">Make the following changes:</span></span>

1. <span data-ttu-id="95d4b-223">Cambio de `ApiController` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="95d4b-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="95d4b-224">Agregue `using Microsoft.AspNetCore.Mvc;` para resolver la `ControllerBase` referencia.</span><span class="sxs-lookup"><span data-stu-id="95d4b-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="95d4b-225">Elimine `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="95d4b-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="95d4b-226">Cambie el `GetProduct` tipo de valor devuelto de la acción de `IHttpActionResult` a `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="95d4b-227">Simplifique la `GetProduct` instrucción de la acción `return` para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="95d4b-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="95d4b-228">Configuración del enrutamiento</span><span class="sxs-lookup"><span data-stu-id="95d4b-228">Configure routing</span></span>

<span data-ttu-id="95d4b-229">Configure el enrutamiento de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="95d4b-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="95d4b-230">Marque la `ProductsController` clase con los siguientes atributos:</span><span class="sxs-lookup"><span data-stu-id="95d4b-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="95d4b-231">El [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura el patrón de enrutamiento de atributo del controlador.</span><span class="sxs-lookup"><span data-stu-id="95d4b-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="95d4b-232">El [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo hace que el enrutamiento de atributos sea un requisito para todas las acciones de este controlador.</span><span class="sxs-lookup"><span data-stu-id="95d4b-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="95d4b-233">El enrutamiento de atributos admite tokens, como `[controller]` y `[action]` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="95d4b-234">En tiempo de ejecución, cada token se reemplaza por el nombre del controlador o la acción, respectivamente, a la que se ha aplicado el atributo.</span><span class="sxs-lookup"><span data-stu-id="95d4b-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="95d4b-235">Los tokens reducen el número de cadenas mágicas del proyecto.</span><span class="sxs-lookup"><span data-stu-id="95d4b-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="95d4b-236">Los tokens también garantizan que las rutas permanezcan sincronizadas con los controladores y acciones correspondientes cuando se apliquen las refactorizaciones de cambio de nombre automáticas.</span><span class="sxs-lookup"><span data-stu-id="95d4b-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="95d4b-237">Establezca el modo de compatibilidad del proyecto en ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="95d4b-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="95d4b-238">El cambio anterior:</span><span class="sxs-lookup"><span data-stu-id="95d4b-238">The preceding change:</span></span>

    * <span data-ttu-id="95d4b-239">Se requiere para usar el `[ApiController]` atributo en el nivel de controlador.</span><span class="sxs-lookup"><span data-stu-id="95d4b-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="95d4b-240">Opta por los posibles saltos de los comportamientos introducidos en ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="95d4b-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="95d4b-241">Habilite las solicitudes HTTP GET para las `ProductController` acciones:</span><span class="sxs-lookup"><span data-stu-id="95d4b-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="95d4b-242">Aplique el [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo a la `GetAllProducts` acción.</span><span class="sxs-lookup"><span data-stu-id="95d4b-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="95d4b-243">Aplique el `[HttpGet("{id}")]` atributo a la `GetProduct` acción.</span><span class="sxs-lookup"><span data-stu-id="95d4b-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="95d4b-244">Ejecute el proyecto migrado y vaya a `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="95d4b-245">Aparece una lista completa de tres productos.</span><span class="sxs-lookup"><span data-stu-id="95d4b-245">A full list of three products appears.</span></span> <span data-ttu-id="95d4b-246">Vaya a `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="95d4b-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="95d4b-247">Aparece el primer producto.</span><span class="sxs-lookup"><span data-stu-id="95d4b-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="95d4b-248">Corrección de compatibilidad</span><span class="sxs-lookup"><span data-stu-id="95d4b-248">Compatibility shim</span></span>

<span data-ttu-id="95d4b-249">La biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) proporciona una corrección de compatibilidad para migrar los proyectos de API Web de ASP.net 4. x a ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="95d4b-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="95d4b-250">La corrección de compatibilidad amplía ASP.NET Core para admitir una serie de convenciones de la API Web de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="95d4b-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="95d4b-251">El ejemplo que se ha migrado anteriormente en este documento es lo suficientemente básico como para que la corrección de compatibilidad no fuera necesaria.</span><span class="sxs-lookup"><span data-stu-id="95d4b-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="95d4b-252">En el caso de los proyectos de mayor tamaño, el uso de la corrección de compatibilidad puede ser útil para el puente temporal entre ASP.NET Core y ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="95d4b-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="95d4b-253">La corrección de compatibilidad de la API Web está pensada para usarse como medida temporal para admitir la migración de proyectos de API Web de ASP.NET 4. x grandes a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95d4b-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="95d4b-254">Con el tiempo, los proyectos deben actualizarse para usar patrones de ASP.NET Core en lugar de basarse en la corrección de compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="95d4b-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="95d4b-255">Las características de compatibilidad incluidas en `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluyen:</span><span class="sxs-lookup"><span data-stu-id="95d4b-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="95d4b-256">Agrega un `ApiController` tipo para que no sea necesario actualizar los tipos base de los controladores.</span><span class="sxs-lookup"><span data-stu-id="95d4b-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="95d4b-257">Habilita el enlace de modelos de estilo de API Web.</span><span class="sxs-lookup"><span data-stu-id="95d4b-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="95d4b-258">ASP.NET Core enlace de modelos de MVC funciona de forma predeterminada con el de ASP.NET 4. x MVC 5.</span><span class="sxs-lookup"><span data-stu-id="95d4b-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="95d4b-259">La corrección de compatibilidad cambia el enlace de modelos para que sea más similar a las convenciones de enlace de modelos de la API Web 2 de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="95d4b-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="95d4b-260">Por ejemplo, los tipos complejos se enlazan automáticamente desde el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="95d4b-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="95d4b-261">Extiende el enlace de modelos para que las acciones de controlador puedan tomar parámetros de tipo `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="95d4b-262">Agrega formateadores de mensajes que permiten que las acciones devuelvan resultados de tipo `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="95d4b-263">Agrega métodos de respuesta adicionales que pueden usar las acciones de Web API 2 para servir respuestas:</span><span class="sxs-lookup"><span data-stu-id="95d4b-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="95d4b-264">`HttpResponseMessage` productores</span><span class="sxs-lookup"><span data-stu-id="95d4b-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="95d4b-265">Métodos del resultado de la acción:</span><span class="sxs-lookup"><span data-stu-id="95d4b-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="95d4b-266">Agrega una instancia de `IContentNegotiator` al contenedor de inserción de dependencias (di) de la aplicación y pone a disposición los tipos relacionados con la negociación de contenido de [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="95d4b-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="95d4b-267">Entre los ejemplos de estos tipos se incluyen `DefaultContentNegotiator` y `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="95d4b-268">Para usar la corrección de compatibilidad:</span><span class="sxs-lookup"><span data-stu-id="95d4b-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="95d4b-269">Instale el paquete NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="95d4b-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="95d4b-270">Registre los servicios de la corrección de compatibilidad con el contenedor de DI de la aplicación mediante una llamada a `services.AddMvc().AddWebApiConventions()` en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="95d4b-271">Defina rutas específicas de la API Web mediante `MapWebApiRoute` en en `IRouteBuilder` la llamada de la aplicación `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="95d4b-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95d4b-272">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="95d4b-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
