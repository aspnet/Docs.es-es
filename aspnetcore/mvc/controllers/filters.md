---
title: Filtros en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo funcionan los filtros y cómo se pueden usar en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
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
uid: mvc/controllers/filters
ms.openlocfilehash: eeae167286e793ecd5a547cea0142cf7d8014ece
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671787"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="8a8fe-103">Filtros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a8fe-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a8fe-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8a8fe-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8a8fe-105">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8a8fe-106">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8a8fe-107">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8a8fe-108">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8a8fe-109">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8a8fe-110">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8a8fe-111">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="8a8fe-112">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8a8fe-113">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="8a8fe-114">Los filtros no funcionan directamente con [ Razor los componentes](xref:blazor/components/index)de.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="8a8fe-115">Un filtro solo puede afectar indirectamente a un componente cuando:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="8a8fe-116">El componente está insertado en una página o vista.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="8a8fe-117">La página o el controlador o la vista utiliza el filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="8a8fe-118">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8a8fe-119">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="8a8fe-119">How filters work</span></span>

<span data-ttu-id="8a8fe-120">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="8a8fe-121">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases Otro middleware, Middleware de enrutamiento, Selección de acción y Canalización de invocación de acción.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8a8fe-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-124">Filter types</span></span>

<span data-ttu-id="8a8fe-125">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8a8fe-126">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8a8fe-127">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="8a8fe-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8a8fe-129">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-129">Run after authorization.</span></span>  
  * <span data-ttu-id="8a8fe-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> ejecuta código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8a8fe-131">Por ejemplo, `OnResourceExecuting` ejecuta código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="8a8fe-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ejecuta el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8a8fe-133">[Filtros de acción](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="8a8fe-134">Ejecutan código inmediatamente antes y después de llamar a un método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="8a8fe-135">Pueden cambiar los argumentos pasados a una acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="8a8fe-136">Pueden cambiar el resultado devuelto de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="8a8fe-137">**No** se admiten en Razor las páginas de.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8a8fe-138">Los [filtros de excepciones](#exception-filters) aplican directivas globales a las excepciones no controladas que se producen antes de que se escriba el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="8a8fe-139">Los [filtros de resultados](#result-filters) ejecutan código inmediatamente antes y después de la ejecución de resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="8a8fe-140">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8a8fe-141">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8a8fe-142">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8a8fe-145">Implementación</span><span class="sxs-lookup"><span data-stu-id="8a8fe-145">Implementation</span></span>

<span data-ttu-id="8a8fe-146">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8a8fe-147">Los filtros sincrónicos ejecutan código antes y después de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="8a8fe-148">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="8a8fe-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8a8fe-150">En el código anterior, [aldebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) es una función de utilidad en la [descarga del ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="8a8fe-151">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="8a8fe-152">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8a8fe-153">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8a8fe-154">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-154">Multiple filter stages</span></span>

<span data-ttu-id="8a8fe-155">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8a8fe-156">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="8a8fe-157">Sincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="8a8fe-158">Asincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="8a8fe-159">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8a8fe-160">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a8fe-161">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a8fe-162">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8a8fe-163">Al usar clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , invalide solo los métodos sincrónicos o los métodos asincrónicos para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8a8fe-164">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="8a8fe-164">Built-in filter attributes</span></span>

<span data-ttu-id="8a8fe-165">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8a8fe-166">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-167">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8a8fe-168">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="8a8fe-169">Use una herramienta como las [herramientas de desarrollo del explorador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="8a8fe-170">En **Encabezados de respuesta**, se muestra `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="8a8fe-171">El código siguiente implementa un atributo `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="8a8fe-172">Lee el título y el nombre del sistema de configuración.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="8a8fe-173">A diferencia del ejemplo anterior, el siguiente código no requiere que se agreguen parámetros de filtro al código.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="8a8fe-174">Agrega el título y el nombre al encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-175">Las opciones de configuración las proporciona el [sistema de configuración](xref:fundamentals/configuration/index) mediante el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8a8fe-176">Por ejemplo, en el archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="8a8fe-177">En `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="8a8fe-178">La clase `PositionOptions` se agrega al contenedor de servicios con el área de configuración `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="8a8fe-179">`MyActionFilterAttribute` se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="8a8fe-180">En el código siguiente se muestra la clase `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="8a8fe-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8a8fe-181">El siguiente código aplica el atributo `MyActionFilterAttribute` al método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="8a8fe-182">En **encabezados de respuesta**, `author: Rick Anderson` y `Editor: Joe Smith` se muestra cuando `Sample/Index2` se llama al extremo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="8a8fe-183">El código siguiente aplica `MyActionFilterAttribute` y `AddHeaderAttribute` a la Razor página:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8a8fe-184">Los filtros no se pueden aplicar a Razor métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="8a8fe-185">Se pueden aplicar al Razor modelo de página o globalmente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="8a8fe-186">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8a8fe-187">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8a8fe-188">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="8a8fe-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="8a8fe-189">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8a8fe-190">Mediante un atributo en una acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="8a8fe-191">Los atributos de filtro no se pueden aplicar a Razor los métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="8a8fe-192">Usar un atributo en un controlador o una Razor página.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="8a8fe-193">Globalmente para todos los controladores, acciones y Razor páginas, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="8a8fe-194">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a8fe-194">Default order of execution</span></span>

<span data-ttu-id="8a8fe-195">Cuando hay varios filtros en una determinada fase de la canalización, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8a8fe-196">Los filtros globales abarcan a los filtros de clase, que a su vez engloban a los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="8a8fe-197">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8a8fe-198">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-198">The filter sequence:</span></span>

* <span data-ttu-id="8a8fe-199">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8a8fe-200">El código *anterior* de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="8a8fe-201">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8a8fe-202">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8a8fe-203">*Después* del código de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="8a8fe-204">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8a8fe-205">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8a8fe-206">Secuencia</span><span class="sxs-lookup"><span data-stu-id="8a8fe-206">Sequence</span></span> | <span data-ttu-id="8a8fe-207">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-207">Filter scope</span></span> | <span data-ttu-id="8a8fe-208">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8a8fe-209">1</span><span class="sxs-lookup"><span data-stu-id="8a8fe-209">1</span></span> | <span data-ttu-id="8a8fe-210">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-211">2</span><span class="sxs-lookup"><span data-stu-id="8a8fe-211">2</span></span> | <span data-ttu-id="8a8fe-212">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="8a8fe-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="8a8fe-213">3</span><span class="sxs-lookup"><span data-stu-id="8a8fe-213">3</span></span> | <span data-ttu-id="8a8fe-214">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-215">4</span><span class="sxs-lookup"><span data-stu-id="8a8fe-215">4</span></span> | <span data-ttu-id="8a8fe-216">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-217">5</span><span class="sxs-lookup"><span data-stu-id="8a8fe-217">5</span></span> | <span data-ttu-id="8a8fe-218">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="8a8fe-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-219">6</span><span class="sxs-lookup"><span data-stu-id="8a8fe-219">6</span></span> | <span data-ttu-id="8a8fe-220">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="8a8fe-221">Filtros de nivel de controlador</span><span class="sxs-lookup"><span data-stu-id="8a8fe-221">Controller level filters</span></span>

<span data-ttu-id="8a8fe-222">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8a8fe-223">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-223">These methods:</span></span>

* <span data-ttu-id="8a8fe-224">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8a8fe-225">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8a8fe-226">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8a8fe-227">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8a8fe-228">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8a8fe-229">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8a8fe-230">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-230">The `TestController`:</span></span>

* <span data-ttu-id="8a8fe-231">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8a8fe-232">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="8a8fe-233">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8a8fe-234">Los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="8a8fe-235">Los filtros de nivel de controlador **no** pueden establecerse para ejecutarse tras aplicarse los filtros a los métodos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="8a8fe-236">Order se explica en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-236">Order is explained in the next section.</span></span>

<span data-ttu-id="8a8fe-237">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8a8fe-238">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a8fe-238">Overriding the default order</span></span>

<span data-ttu-id="8a8fe-239">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8a8fe-240"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8a8fe-241">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8a8fe-242">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8a8fe-243">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8a8fe-244">La propiedad `Order` se establece con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="8a8fe-245">Tenga en cuenta los dos filtros de acción en el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8a8fe-246">Un filtro global se agrega en `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="8a8fe-247">Los tres filtros se ejecutan en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="8a8fe-248">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8a8fe-249">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8a8fe-250">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8a8fe-251">Como se mencionó anteriormente, los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. En el caso de los filtros integrados, el ámbito determina el orden a menos que se establezca `Order` en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="8a8fe-252">En el código anterior, `MySampleActionFilter` tiene ámbito global, por lo que se ejecuta antes de `MyAction2FilterAttribute`, que tiene ámbito de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="8a8fe-253">Para que `MyAction2FilterAttribute` se ejecute en primer lugar, establezca el orden en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="8a8fe-254">Para que el filtro global `MySampleActionFilter` se ejecute en primer lugar, establezca `Order` en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8a8fe-255">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="8a8fe-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8a8fe-256">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8a8fe-257">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-258">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8a8fe-259">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8a8fe-260">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8a8fe-261">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8a8fe-262">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8a8fe-263">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8a8fe-264">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="8a8fe-265">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="8a8fe-265">Dependency injection</span></span>

<span data-ttu-id="8a8fe-266">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8a8fe-267">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8a8fe-268">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8a8fe-269">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-269">A type-activated filter means:</span></span>

* <span data-ttu-id="8a8fe-270">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-270">An instance is created for each request.</span></span>
* <span data-ttu-id="8a8fe-271">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8a8fe-272">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8a8fe-273">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8a8fe-274">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8a8fe-275">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8a8fe-276">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8a8fe-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8a8fe-278">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8a8fe-279">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-279">Loggers are available from DI.</span></span> <span data-ttu-id="8a8fe-280">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8a8fe-281">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8a8fe-282">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-282">Logging added to filters:</span></span>

* <span data-ttu-id="8a8fe-283">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8a8fe-284">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8a8fe-285">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8a8fe-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8a8fe-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="8a8fe-287">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8a8fe-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8a8fe-289">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8a8fe-290">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="8a8fe-291">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8a8fe-292">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8a8fe-293">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8a8fe-294">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8a8fe-295">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8a8fe-296">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8a8fe-297">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8a8fe-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8a8fe-299">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8a8fe-300">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8a8fe-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8a8fe-301">TypeFilterAttribute</span></span>

<span data-ttu-id="8a8fe-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="8a8fe-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8a8fe-303">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8a8fe-304">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8a8fe-305">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8a8fe-306">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8a8fe-307">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8a8fe-308">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8a8fe-309">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8a8fe-310">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8a8fe-311">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8a8fe-312">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8a8fe-313">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="8a8fe-313">Authorization filters</span></span>

<span data-ttu-id="8a8fe-314">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-314">Authorization filters:</span></span>

* <span data-ttu-id="8a8fe-315">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8a8fe-316">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-316">Control access to action methods.</span></span>
* <span data-ttu-id="8a8fe-317">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="8a8fe-318">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8a8fe-319">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8a8fe-320">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="8a8fe-321">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-321">Calls the authorization system.</span></span>
* <span data-ttu-id="8a8fe-322">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-322">Does not authorize requests.</span></span>

<span data-ttu-id="8a8fe-323">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8a8fe-324">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-324">The exception will not be handled.</span></span>
* <span data-ttu-id="8a8fe-325">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8a8fe-326">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8a8fe-327">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8a8fe-328">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="8a8fe-328">Resource filters</span></span>

<span data-ttu-id="8a8fe-329">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-329">Resource filters:</span></span>

* <span data-ttu-id="8a8fe-330">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8a8fe-331">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8a8fe-332">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8a8fe-333">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8a8fe-334">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8a8fe-335">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-335">Resource filter examples:</span></span>

* <span data-ttu-id="8a8fe-336">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8a8fe-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8a8fe-338">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8a8fe-339">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8a8fe-340">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-340">Action filters</span></span>

<span data-ttu-id="8a8fe-341">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8a8fe-342">Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8a8fe-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8a8fe-343">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8a8fe-344">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-344">Action filters:</span></span>

* <span data-ttu-id="8a8fe-345">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8a8fe-346">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8a8fe-347">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8a8fe-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8a8fe-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="8a8fe-350"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8a8fe-351"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8a8fe-352">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8a8fe-353">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8a8fe-354">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8a8fe-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8a8fe-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8a8fe-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8a8fe-358">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-358">Setting this property to null:</span></span>

  * <span data-ttu-id="8a8fe-359">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8a8fe-360">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8a8fe-361">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8a8fe-362">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8a8fe-363">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8a8fe-364">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8a8fe-365">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8a8fe-366">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8a8fe-367">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-367">Validate model state.</span></span>
* <span data-ttu-id="8a8fe-368">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="8a8fe-369">Los controladores anotados con el `[ApiController]` atributo validan automáticamente el estado del modelo y devuelven una respuesta 400.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="8a8fe-370">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="8a8fe-371">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8a8fe-372">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8a8fe-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8a8fe-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8a8fe-375">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8a8fe-376">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8a8fe-377">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8a8fe-378">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-378">Exception filters</span></span>

<span data-ttu-id="8a8fe-379">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-379">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-380">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="8a8fe-381">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8a8fe-382">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8a8fe-383">El código siguiente prueba el filtro de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="8a8fe-384">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-384">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-385">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-385">Don't have before and after events.</span></span>
* <span data-ttu-id="8a8fe-386">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8a8fe-387">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8a8fe-388">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8a8fe-389">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8a8fe-390">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-390">This stops propagation of the exception.</span></span> <span data-ttu-id="8a8fe-391">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="8a8fe-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8a8fe-392">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-392">Only an action filter can do that.</span></span>

<span data-ttu-id="8a8fe-393">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-393">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-394">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8a8fe-395">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8a8fe-396">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8a8fe-397">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8a8fe-398">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8a8fe-399">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8a8fe-400">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="8a8fe-400">Result filters</span></span>

<span data-ttu-id="8a8fe-401">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-401">Result filters:</span></span>

* <span data-ttu-id="8a8fe-402">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-402">Implement an interface:</span></span>
  * <span data-ttu-id="8a8fe-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8a8fe-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8a8fe-405">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8a8fe-406">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8a8fe-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8a8fe-407">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8a8fe-408">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8a8fe-409">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8a8fe-410">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8a8fe-411">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8a8fe-412">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8a8fe-413">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="8a8fe-414">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8a8fe-415">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8a8fe-416">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8a8fe-417">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8a8fe-418">Generación de una excepción en `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="8a8fe-419">Impide la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8a8fe-420">Se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8a8fe-421">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="8a8fe-422">En este caso, no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="8a8fe-423">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8a8fe-424">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8a8fe-425">Establecer `Exception` en un valor null hace que una excepción se controle de forma eficaz y evita que se vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="8a8fe-426">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8a8fe-427">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8a8fe-428">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8a8fe-429">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8a8fe-430">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8a8fe-431">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8a8fe-432">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8a8fe-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8a8fe-433">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8a8fe-434">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-434">This includes action results produced by:</span></span>

* <span data-ttu-id="8a8fe-435">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8a8fe-436">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-436">Exception filters.</span></span>

<span data-ttu-id="8a8fe-437">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8a8fe-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8a8fe-438">IFilterFactory</span></span>

<span data-ttu-id="8a8fe-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8a8fe-440">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8a8fe-441">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8a8fe-442">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8a8fe-443">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8a8fe-444">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8a8fe-445">El filtro se aplica en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="8a8fe-446">Pruebe el código anterior mediante la ejecución del [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="8a8fe-447">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8a8fe-448">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8a8fe-449">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8a8fe-450">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="8a8fe-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8a8fe-452">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-452">**internal:** `My header`</span></span>

<span data-ttu-id="8a8fe-453">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8a8fe-454">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="8a8fe-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8a8fe-455">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8a8fe-456">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="8a8fe-457">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8a8fe-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8a8fe-459">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8a8fe-460">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8a8fe-461">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8a8fe-462">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8a8fe-463">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8a8fe-464">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8a8fe-465">Pero los filtros se diferencian del middleware en que forman parte del runtime, lo que significa que tienen acceso al contexto y las construcciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="8a8fe-466">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8a8fe-467">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8a8fe-468">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8a8fe-469">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8a8fe-470">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-470">Next actions</span></span>

* <span data-ttu-id="8a8fe-471">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8a8fe-472">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a8fe-473">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8a8fe-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8a8fe-474">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8a8fe-475">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8a8fe-476">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8a8fe-477">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8a8fe-478">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8a8fe-479">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8a8fe-480">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="8a8fe-481">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8a8fe-482">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="8a8fe-483">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8a8fe-484">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="8a8fe-484">How filters work</span></span>

<span data-ttu-id="8a8fe-485">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="8a8fe-486">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases de otro middleware, del middleware de enrutamiento, de la selección de acción y de la canalización de invocación de acciones de ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8a8fe-489">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-489">Filter types</span></span>

<span data-ttu-id="8a8fe-490">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8a8fe-491">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8a8fe-492">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="8a8fe-493">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8a8fe-494">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-494">Run after authorization.</span></span>  
  * <span data-ttu-id="8a8fe-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> puede ejecutar código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8a8fe-496">Por ejemplo, `OnResourceExecuting` puede ejecutar código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="8a8fe-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> puede ejecutar el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8a8fe-498">Los [filtros de acciones](#action-filters) pueden ejecutar código inmediatamente antes y después de llamar a un método de acción individual.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="8a8fe-499">Se pueden usar para manipular los argumentos pasados a una acción y el resultado obtenido de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="8a8fe-500">**No** se admiten filtros de acción en Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8a8fe-501">Los [filtros de excepciones](#exception-filters) sirven para aplicar directivas globales a las excepciones no controladas que se producen antes de que se escriba algo en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="8a8fe-502">Los [filtros de resultados](#result-filters) pueden ejecutar código inmediatamente antes y después de la ejecución de resultados de acción individuales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="8a8fe-503">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8a8fe-504">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8a8fe-505">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8a8fe-508">Implementación</span><span class="sxs-lookup"><span data-stu-id="8a8fe-508">Implementation</span></span>

<span data-ttu-id="8a8fe-509">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8a8fe-510">Los filtros sincrónicos pueden ejecutar código antes (`On-Stage-Executing`) y después (`On-Stage-Executed`) de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="8a8fe-511">Por ejemplo, `OnActionExecuting` se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="8a8fe-512">`OnActionExecuted` se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8a8fe-513">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8a8fe-514">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="8a8fe-515">Cada uno de los métodos `On-Stage-ExecutionAsync` toman un `FilterType-ExecutionDelegate` que ejecuta la fase de canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8a8fe-516">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-516">Multiple filter stages</span></span>

<span data-ttu-id="8a8fe-517">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8a8fe-518">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` y sus equivalentes asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="8a8fe-519">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8a8fe-520">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a8fe-521">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a8fe-522">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8a8fe-523">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8a8fe-524">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="8a8fe-524">Built-in filter attributes</span></span>

<span data-ttu-id="8a8fe-525">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8a8fe-526">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-527">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8a8fe-528">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="8a8fe-529">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8a8fe-530">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8a8fe-531">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="8a8fe-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="8a8fe-532">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8a8fe-533">Mediante un atributo en una acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="8a8fe-534">Mediante un atributo en un controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="8a8fe-535">Globalmente para todos los controladores y las acciones, tal como se muestra en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8a8fe-536">El código anterior agrega tres filtros globalmente mediante la colección [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="8a8fe-537">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a8fe-537">Default order of execution</span></span>

<span data-ttu-id="8a8fe-538">Cuando hay varios filtros *del mismo tipo*, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8a8fe-539">Los filtros globales delimitan los filtros de clase.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-539">Global filters surround class filters.</span></span> <span data-ttu-id="8a8fe-540">Los filtros de clase delimitan los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-540">Class filters surround method filters.</span></span>

<span data-ttu-id="8a8fe-541">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8a8fe-542">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-542">The filter sequence:</span></span>

* <span data-ttu-id="8a8fe-543">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8a8fe-544">El código *anterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="8a8fe-545">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8a8fe-546">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8a8fe-547">El código *posterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="8a8fe-548">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8a8fe-549">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8a8fe-550">Secuencia</span><span class="sxs-lookup"><span data-stu-id="8a8fe-550">Sequence</span></span> | <span data-ttu-id="8a8fe-551">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-551">Filter scope</span></span> | <span data-ttu-id="8a8fe-552">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8a8fe-553">1</span><span class="sxs-lookup"><span data-stu-id="8a8fe-553">1</span></span> | <span data-ttu-id="8a8fe-554">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-555">2</span><span class="sxs-lookup"><span data-stu-id="8a8fe-555">2</span></span> | <span data-ttu-id="8a8fe-556">Controller</span><span class="sxs-lookup"><span data-stu-id="8a8fe-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-557">3</span><span class="sxs-lookup"><span data-stu-id="8a8fe-557">3</span></span> | <span data-ttu-id="8a8fe-558">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-559">4</span><span class="sxs-lookup"><span data-stu-id="8a8fe-559">4</span></span> | <span data-ttu-id="8a8fe-560">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-561">5</span><span class="sxs-lookup"><span data-stu-id="8a8fe-561">5</span></span> | <span data-ttu-id="8a8fe-562">Controller</span><span class="sxs-lookup"><span data-stu-id="8a8fe-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-563">6</span><span class="sxs-lookup"><span data-stu-id="8a8fe-563">6</span></span> | <span data-ttu-id="8a8fe-564">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="8a8fe-565">Esta secuencia pone de manifiesto lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-565">This sequence shows:</span></span>

* <span data-ttu-id="8a8fe-566">El filtro de método está anidado en el filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="8a8fe-567">El filtro de controlador está anidado en el filtro global.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="8a8fe-568">Filtros de Razor nivel de página y controlador</span><span class="sxs-lookup"><span data-stu-id="8a8fe-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="8a8fe-569">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8a8fe-570">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-570">These methods:</span></span>

* <span data-ttu-id="8a8fe-571">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8a8fe-572">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8a8fe-573">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8a8fe-574">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8a8fe-575">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8a8fe-576">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8a8fe-577">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-577">The `TestController`:</span></span>

* <span data-ttu-id="8a8fe-578">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8a8fe-579">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8a8fe-580">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8a8fe-581">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8a8fe-582">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="8a8fe-582">Overriding the default order</span></span>

<span data-ttu-id="8a8fe-583">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8a8fe-584"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8a8fe-585">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8a8fe-586">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8a8fe-587">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8a8fe-588">La propiedad `Order` se puede establecer con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="8a8fe-589">Considere los mismos tres filtros de acción que se muestran en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="8a8fe-590">Si la propiedad `Order` del controlador y de los filtros globales está establecida en 1 y 2 respectivamente, el orden de ejecución se invierte.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="8a8fe-591">Secuencia</span><span class="sxs-lookup"><span data-stu-id="8a8fe-591">Sequence</span></span> | <span data-ttu-id="8a8fe-592">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-592">Filter scope</span></span> | <span data-ttu-id="8a8fe-593">Propiedad`Order`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-593">`Order` property</span></span> | <span data-ttu-id="8a8fe-594">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="8a8fe-595">1</span><span class="sxs-lookup"><span data-stu-id="8a8fe-595">1</span></span> | <span data-ttu-id="8a8fe-596">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-596">Method</span></span> | <span data-ttu-id="8a8fe-597">0</span><span class="sxs-lookup"><span data-stu-id="8a8fe-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-598">2</span><span class="sxs-lookup"><span data-stu-id="8a8fe-598">2</span></span> | <span data-ttu-id="8a8fe-599">Controller</span><span class="sxs-lookup"><span data-stu-id="8a8fe-599">Controller</span></span> | <span data-ttu-id="8a8fe-600">1</span><span class="sxs-lookup"><span data-stu-id="8a8fe-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-601">3</span><span class="sxs-lookup"><span data-stu-id="8a8fe-601">3</span></span> | <span data-ttu-id="8a8fe-602">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-602">Global</span></span> | <span data-ttu-id="8a8fe-603">2</span><span class="sxs-lookup"><span data-stu-id="8a8fe-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8a8fe-604">4</span><span class="sxs-lookup"><span data-stu-id="8a8fe-604">4</span></span> | <span data-ttu-id="8a8fe-605">Global</span><span class="sxs-lookup"><span data-stu-id="8a8fe-605">Global</span></span> | <span data-ttu-id="8a8fe-606">2</span><span class="sxs-lookup"><span data-stu-id="8a8fe-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-607">5</span><span class="sxs-lookup"><span data-stu-id="8a8fe-607">5</span></span> | <span data-ttu-id="8a8fe-608">Controller</span><span class="sxs-lookup"><span data-stu-id="8a8fe-608">Controller</span></span> | <span data-ttu-id="8a8fe-609">1</span><span class="sxs-lookup"><span data-stu-id="8a8fe-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8a8fe-610">6</span><span class="sxs-lookup"><span data-stu-id="8a8fe-610">6</span></span> | <span data-ttu-id="8a8fe-611">Método</span><span class="sxs-lookup"><span data-stu-id="8a8fe-611">Method</span></span> | <span data-ttu-id="8a8fe-612">0</span><span class="sxs-lookup"><span data-stu-id="8a8fe-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="8a8fe-613">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8a8fe-614">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8a8fe-615">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8a8fe-616">En los filtros integrados, el ámbito determina el orden, a menos que `Order` se establezca en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8a8fe-617">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="8a8fe-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8a8fe-618">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8a8fe-619">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-620">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8a8fe-621">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8a8fe-622">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8a8fe-623">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8a8fe-624">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8a8fe-625">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8a8fe-626">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="8a8fe-627">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="8a8fe-627">Dependency injection</span></span>

<span data-ttu-id="8a8fe-628">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8a8fe-629">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8a8fe-630">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8a8fe-631">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-631">A type-activated filter means:</span></span>

* <span data-ttu-id="8a8fe-632">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-632">An instance is created for each request.</span></span>
* <span data-ttu-id="8a8fe-633">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8a8fe-634">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8a8fe-635">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8a8fe-636">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8a8fe-637">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8a8fe-638">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8a8fe-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8a8fe-640">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8a8fe-641">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-641">Loggers are available from DI.</span></span> <span data-ttu-id="8a8fe-642">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8a8fe-643">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8a8fe-644">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-644">Logging added to filters:</span></span>

* <span data-ttu-id="8a8fe-645">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8a8fe-646">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8a8fe-647">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8a8fe-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8a8fe-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="8a8fe-649">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8a8fe-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8a8fe-651">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8a8fe-652">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8a8fe-653">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8a8fe-654">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8a8fe-655">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8a8fe-656">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8a8fe-657">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8a8fe-658">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8a8fe-659">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8a8fe-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8a8fe-661">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8a8fe-662">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8a8fe-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8a8fe-663">TypeFilterAttribute</span></span>

<span data-ttu-id="8a8fe-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="8a8fe-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8a8fe-665">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8a8fe-666">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8a8fe-667">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8a8fe-668">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8a8fe-669">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8a8fe-670">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8a8fe-671">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8a8fe-672">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8a8fe-673">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8a8fe-674">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8a8fe-675">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="8a8fe-675">Authorization filters</span></span>

<span data-ttu-id="8a8fe-676">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-676">Authorization filters:</span></span>

* <span data-ttu-id="8a8fe-677">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8a8fe-678">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-678">Control access to action methods.</span></span>
* <span data-ttu-id="8a8fe-679">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="8a8fe-680">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8a8fe-681">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8a8fe-682">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="8a8fe-683">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-683">Calls the authorization system.</span></span>
* <span data-ttu-id="8a8fe-684">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-684">Does not authorize requests.</span></span>

<span data-ttu-id="8a8fe-685">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8a8fe-686">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-686">The exception will not be handled.</span></span>
* <span data-ttu-id="8a8fe-687">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8a8fe-688">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8a8fe-689">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8a8fe-690">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="8a8fe-690">Resource filters</span></span>

<span data-ttu-id="8a8fe-691">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-691">Resource filters:</span></span>

* <span data-ttu-id="8a8fe-692">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8a8fe-693">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8a8fe-694">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8a8fe-695">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8a8fe-696">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8a8fe-697">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-697">Resource filter examples:</span></span>

* <span data-ttu-id="8a8fe-698">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8a8fe-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8a8fe-700">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8a8fe-701">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8a8fe-702">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a8fe-703">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8a8fe-704">Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8a8fe-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8a8fe-705">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8a8fe-706">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-706">Action filters:</span></span>

* <span data-ttu-id="8a8fe-707">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8a8fe-708">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8a8fe-709">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8a8fe-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8a8fe-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="8a8fe-712"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8a8fe-713"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8a8fe-714">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8a8fe-715">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8a8fe-716">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8a8fe-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8a8fe-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8a8fe-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8a8fe-720">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-720">Setting this property to null:</span></span>

  * <span data-ttu-id="8a8fe-721">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8a8fe-722">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8a8fe-723">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8a8fe-724">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8a8fe-725">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8a8fe-726">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8a8fe-727">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8a8fe-728">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8a8fe-729">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-729">Validate model state.</span></span>
* <span data-ttu-id="8a8fe-730">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="8a8fe-731">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8a8fe-732">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8a8fe-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8a8fe-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8a8fe-735">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8a8fe-736">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8a8fe-737">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8a8fe-738">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-738">Exception filters</span></span>

<span data-ttu-id="8a8fe-739">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-739">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-740">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="8a8fe-741">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8a8fe-742">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8a8fe-743">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-743">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-744">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-744">Don't have before and after events.</span></span>
* <span data-ttu-id="8a8fe-745">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8a8fe-746">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8a8fe-747">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8a8fe-748">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8a8fe-749">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-749">This stops propagation of the exception.</span></span> <span data-ttu-id="8a8fe-750">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="8a8fe-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8a8fe-751">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-751">Only an action filter can do that.</span></span>

<span data-ttu-id="8a8fe-752">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-752">Exception filters:</span></span>

* <span data-ttu-id="8a8fe-753">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8a8fe-754">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8a8fe-755">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8a8fe-756">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8a8fe-757">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8a8fe-758">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8a8fe-759">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="8a8fe-759">Result filters</span></span>

<span data-ttu-id="8a8fe-760">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-760">Result filters:</span></span>

* <span data-ttu-id="8a8fe-761">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-761">Implement an interface:</span></span>
  * <span data-ttu-id="8a8fe-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8a8fe-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8a8fe-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8a8fe-764">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8a8fe-765">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8a8fe-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8a8fe-766">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8a8fe-767">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8a8fe-768">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8a8fe-769">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8a8fe-770">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8a8fe-771">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8a8fe-772">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="8a8fe-773">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8a8fe-774">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8a8fe-775">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8a8fe-776">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8a8fe-777">Si se inicia una excepción en `IResultFilter.OnResultExecuting`, sucederá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="8a8fe-778">Se impedirá la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8a8fe-779">Se tratará como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8a8fe-780">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="8a8fe-781">En este caso, no se puede cambiar más.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="8a8fe-782">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8a8fe-783">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8a8fe-784">Establecer `Exception` en un valor NULL hace que una excepción se "controle" de forma eficaz y evita que ASP.NET Core vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="8a8fe-785">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8a8fe-786">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8a8fe-787">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8a8fe-788">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8a8fe-789">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8a8fe-790">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8a8fe-791">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8a8fe-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8a8fe-792">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8a8fe-793">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-793">This includes action results produced by:</span></span>

* <span data-ttu-id="8a8fe-794">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8a8fe-795">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-795">Exception filters.</span></span>

<span data-ttu-id="8a8fe-796">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8a8fe-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8a8fe-797">IFilterFactory</span></span>

<span data-ttu-id="8a8fe-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8a8fe-799">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8a8fe-800">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8a8fe-801">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8a8fe-802">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8a8fe-803">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8a8fe-804">El código anterior se puede probar mediante la ejecución del [ejemplo de descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="8a8fe-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="8a8fe-805">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8a8fe-806">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8a8fe-807">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8a8fe-808">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="8a8fe-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8a8fe-810">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8a8fe-810">**internal:** `My header`</span></span>

<span data-ttu-id="8a8fe-811">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8a8fe-812">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="8a8fe-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8a8fe-813">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8a8fe-814">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="8a8fe-815">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8a8fe-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8a8fe-817">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8a8fe-818">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8a8fe-819">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8a8fe-820">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8a8fe-821">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="8a8fe-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8a8fe-822">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8a8fe-823">Pero los filtros se diferencian del middleware en que forman parte del entorno de ejecución de ASP.NET Core, lo que significa que tienen acceso al contexto y las construcciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="8a8fe-824">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8a8fe-825">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8a8fe-826">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="8a8fe-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8a8fe-827">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="8a8fe-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8a8fe-828">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="8a8fe-828">Next actions</span></span>

* <span data-ttu-id="8a8fe-829">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8a8fe-830">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="8a8fe-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
