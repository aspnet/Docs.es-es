---
title: Filtros en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo funcionan los filtros y cómo se pueden usar en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: d075faa951a34fb3856b54eb9e21593b6616b4f1
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673970"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="ddeab-103">Filtros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddeab-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ddeab-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ddeab-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ddeab-105">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ddeab-106">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="ddeab-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ddeab-107">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="ddeab-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ddeab-108">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="ddeab-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ddeab-109">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ddeab-110">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ddeab-111">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="ddeab-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="ddeab-112">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ddeab-113">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="ddeab-114">Los filtros no funcionan directamente con [ Razor los componentes](xref:blazor/components/index)de.</span><span class="sxs-lookup"><span data-stu-id="ddeab-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="ddeab-115">Un filtro solo puede afectar indirectamente a un componente cuando:</span><span class="sxs-lookup"><span data-stu-id="ddeab-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="ddeab-116">El componente está insertado en una página o vista.</span><span class="sxs-lookup"><span data-stu-id="ddeab-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="ddeab-117">La página o el controlador o la vista utiliza el filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="ddeab-118">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ddeab-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ddeab-119">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="ddeab-119">How filters work</span></span>

<span data-ttu-id="ddeab-120">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="ddeab-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="ddeab-121">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases Otro middleware, Middleware de enrutamiento, Selección de acción y Canalización de invocación de acción.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ddeab-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-124">Filter types</span></span>

<span data-ttu-id="ddeab-125">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ddeab-126">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ddeab-127">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="ddeab-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="ddeab-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ddeab-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ddeab-129">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-129">Run after authorization.</span></span>  
  * <span data-ttu-id="ddeab-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> ejecuta código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ddeab-131">Por ejemplo, `OnResourceExecuting` ejecuta código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="ddeab-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ejecuta el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ddeab-133">[Filtros de acción](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="ddeab-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="ddeab-134">Ejecutan código inmediatamente antes y después de llamar a un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="ddeab-135">Pueden cambiar los argumentos pasados a una acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="ddeab-136">Pueden cambiar el resultado devuelto de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="ddeab-137">**No** se admiten en Razor las páginas de.</span><span class="sxs-lookup"><span data-stu-id="ddeab-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ddeab-138">Los [filtros de excepciones](#exception-filters) aplican directivas globales a las excepciones no controladas que se producen antes de que se escriba el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ddeab-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="ddeab-139">Los [filtros de resultados](#result-filters) ejecutan código inmediatamente antes y después de la ejecución de resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="ddeab-140">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ddeab-141">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ddeab-142">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ddeab-145">Implementación</span><span class="sxs-lookup"><span data-stu-id="ddeab-145">Implementation</span></span>

<span data-ttu-id="ddeab-146">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="ddeab-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ddeab-147">Los filtros sincrónicos ejecutan código antes y después de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="ddeab-148">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="ddeab-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddeab-150">En el código anterior, [aldebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) es una función de utilidad en la [descarga del ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="ddeab-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="ddeab-151">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="ddeab-152">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="ddeab-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ddeab-153">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ddeab-154">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-154">Multiple filter stages</span></span>

<span data-ttu-id="ddeab-155">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ddeab-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ddeab-156">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="ddeab-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="ddeab-157">Sincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="ddeab-158">Asincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="ddeab-159">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ddeab-160">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="ddeab-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ddeab-161">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ddeab-162">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="ddeab-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ddeab-163">Al usar clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , invalide solo los métodos sincrónicos o los métodos asincrónicos para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ddeab-164">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="ddeab-164">Built-in filter attributes</span></span>

<span data-ttu-id="ddeab-165">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ddeab-166">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ddeab-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-167">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ddeab-168">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddeab-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="ddeab-169">Use una herramienta como las [herramientas de desarrollo del explorador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="ddeab-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="ddeab-170">En **Encabezados de respuesta**, se muestra `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="ddeab-171">El código siguiente implementa un atributo `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="ddeab-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="ddeab-172">Lee el título y el nombre del sistema de configuración.</span><span class="sxs-lookup"><span data-stu-id="ddeab-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="ddeab-173">A diferencia del ejemplo anterior, el siguiente código no requiere que se agreguen parámetros de filtro al código.</span><span class="sxs-lookup"><span data-stu-id="ddeab-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="ddeab-174">Agrega el título y el nombre al encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ddeab-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-175">Las opciones de configuración las proporciona el [sistema de configuración](xref:fundamentals/configuration/index) mediante el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ddeab-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ddeab-176">Por ejemplo, desde el *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="ddeab-177">En `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="ddeab-178">La clase `PositionOptions` se agrega al contenedor de servicios con el área de configuración `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="ddeab-179">`MyActionFilterAttribute` se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ddeab-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="ddeab-180">En el código siguiente se muestra la clase `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="ddeab-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="ddeab-181">El siguiente código aplica el atributo `MyActionFilterAttribute` al método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="ddeab-182">En **encabezados de respuesta**, `author: Rick Anderson` y `Editor: Joe Smith` se muestra cuando `Sample/Index2` se llama al extremo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="ddeab-183">El código siguiente aplica `MyActionFilterAttribute` y `AddHeaderAttribute` a la Razor página:</span><span class="sxs-lookup"><span data-stu-id="ddeab-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ddeab-184">Los filtros no se pueden aplicar a Razor métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="ddeab-185">Se pueden aplicar al Razor modelo de página o globalmente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="ddeab-186">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ddeab-187">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ddeab-188">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="ddeab-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="ddeab-189">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="ddeab-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ddeab-190">Mediante un atributo en una acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="ddeab-191">Los atributos de filtro no se pueden aplicar a Razor los métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="ddeab-192">Usar un atributo en un controlador o una Razor página.</span><span class="sxs-lookup"><span data-stu-id="ddeab-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="ddeab-193">Globalmente para todos los controladores, acciones y Razor páginas, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="ddeab-194">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="ddeab-194">Default order of execution</span></span>

<span data-ttu-id="ddeab-195">Cuando hay varios filtros en una determinada fase de la canalización, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ddeab-196">Los filtros globales abarcan a los filtros de clase, que a su vez engloban a los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="ddeab-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="ddeab-197">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="ddeab-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ddeab-198">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-198">The filter sequence:</span></span>

* <span data-ttu-id="ddeab-199">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ddeab-200">El código *anterior* de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="ddeab-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="ddeab-201">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ddeab-202">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ddeab-203">*Después* del código de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="ddeab-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="ddeab-204">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ddeab-205">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ddeab-206">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ddeab-206">Sequence</span></span> | <span data-ttu-id="ddeab-207">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-207">Filter scope</span></span> | <span data-ttu-id="ddeab-208">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ddeab-209">1</span><span class="sxs-lookup"><span data-stu-id="ddeab-209">1</span></span> | <span data-ttu-id="ddeab-210">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-211">2</span><span class="sxs-lookup"><span data-stu-id="ddeab-211">2</span></span> | <span data-ttu-id="ddeab-212">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="ddeab-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="ddeab-213">3</span><span class="sxs-lookup"><span data-stu-id="ddeab-213">3</span></span> | <span data-ttu-id="ddeab-214">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-215">4</span><span class="sxs-lookup"><span data-stu-id="ddeab-215">4</span></span> | <span data-ttu-id="ddeab-216">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddeab-217">5</span><span class="sxs-lookup"><span data-stu-id="ddeab-217">5</span></span> | <span data-ttu-id="ddeab-218">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="ddeab-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddeab-219">6</span><span class="sxs-lookup"><span data-stu-id="ddeab-219">6</span></span> | <span data-ttu-id="ddeab-220">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="ddeab-221">Filtros de nivel de controlador</span><span class="sxs-lookup"><span data-stu-id="ddeab-221">Controller level filters</span></span>

<span data-ttu-id="ddeab-222">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ddeab-223">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-223">These methods:</span></span>

* <span data-ttu-id="ddeab-224">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="ddeab-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ddeab-225">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ddeab-226">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ddeab-227">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ddeab-228">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ddeab-229">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="ddeab-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ddeab-230">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-230">The `TestController`:</span></span>

* <span data-ttu-id="ddeab-231">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ddeab-232">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="ddeab-233">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ddeab-234">Los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="ddeab-235">Los filtros de nivel de controlador **no** pueden establecerse para ejecutarse tras aplicarse los filtros a los métodos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="ddeab-236">Order se explica en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-236">Order is explained in the next section.</span></span>

<span data-ttu-id="ddeab-237">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ddeab-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ddeab-238">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="ddeab-238">Overriding the default order</span></span>

<span data-ttu-id="ddeab-239">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ddeab-240"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ddeab-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ddeab-241">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="ddeab-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ddeab-242">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ddeab-243">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ddeab-244">La propiedad `Order` se establece con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="ddeab-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="ddeab-245">Tenga en cuenta los dos filtros de acción en el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="ddeab-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ddeab-246">Un filtro global se agrega en `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="ddeab-247">Los tres filtros se ejecutan en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="ddeab-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="ddeab-248">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="ddeab-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ddeab-249">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="ddeab-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ddeab-250">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="ddeab-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ddeab-251">Como se mencionó anteriormente, los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. En el caso de los filtros integrados, el ámbito determina el orden a menos que se establezca `Order` en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="ddeab-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="ddeab-252">En el código anterior, `MySampleActionFilter` tiene ámbito global, por lo que se ejecuta antes de `MyAction2FilterAttribute`, que tiene ámbito de controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="ddeab-253">Para que `MyAction2FilterAttribute` se ejecute en primer lugar, establezca el orden en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="ddeab-254">Para que el filtro global `MySampleActionFilter` se ejecute en primer lugar, establezca `Order` en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ddeab-255">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="ddeab-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ddeab-256">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ddeab-257">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="ddeab-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-258">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ddeab-259">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ddeab-260">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ddeab-261">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ddeab-262">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ddeab-263">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="ddeab-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ddeab-264">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="ddeab-265">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ddeab-265">Dependency injection</span></span>

<span data-ttu-id="ddeab-266">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="ddeab-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ddeab-267">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ddeab-268">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ddeab-269">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="ddeab-269">A type-activated filter means:</span></span>

* <span data-ttu-id="ddeab-270">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-270">An instance is created for each request.</span></span>
* <span data-ttu-id="ddeab-271">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ddeab-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ddeab-272">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ddeab-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ddeab-273">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="ddeab-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ddeab-274">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="ddeab-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ddeab-275">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ddeab-276">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ddeab-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ddeab-278">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ddeab-279">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-279">Loggers are available from DI.</span></span> <span data-ttu-id="ddeab-280">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ddeab-281">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ddeab-282">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="ddeab-282">Logging added to filters:</span></span>

* <span data-ttu-id="ddeab-283">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ddeab-284">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ddeab-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ddeab-285">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ddeab-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ddeab-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddeab-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="ddeab-287">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ddeab-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ddeab-289">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddeab-290">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="ddeab-291">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ddeab-292">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddeab-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ddeab-293">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ddeab-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddeab-294">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="ddeab-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ddeab-295">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ddeab-296">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ddeab-297">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ddeab-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ddeab-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddeab-299">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddeab-300">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ddeab-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddeab-301">TypeFilterAttribute</span></span>

<span data-ttu-id="ddeab-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="ddeab-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ddeab-303">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ddeab-304">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ddeab-305">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ddeab-306">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ddeab-307">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="ddeab-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ddeab-308">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddeab-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ddeab-309">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ddeab-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddeab-310">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ddeab-311">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ddeab-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ddeab-312">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ddeab-313">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="ddeab-313">Authorization filters</span></span>

<span data-ttu-id="ddeab-314">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ddeab-314">Authorization filters:</span></span>

* <span data-ttu-id="ddeab-315">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ddeab-316">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-316">Control access to action methods.</span></span>
* <span data-ttu-id="ddeab-317">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="ddeab-318">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ddeab-319">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ddeab-320">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="ddeab-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="ddeab-321">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="ddeab-321">Calls the authorization system.</span></span>
* <span data-ttu-id="ddeab-322">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-322">Does not authorize requests.</span></span>

<span data-ttu-id="ddeab-323">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ddeab-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ddeab-324">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="ddeab-324">The exception will not be handled.</span></span>
* <span data-ttu-id="ddeab-325">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ddeab-326">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ddeab-327">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ddeab-328">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="ddeab-328">Resource filters</span></span>

<span data-ttu-id="ddeab-329">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-329">Resource filters:</span></span>

* <span data-ttu-id="ddeab-330">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ddeab-331">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ddeab-332">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ddeab-333">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ddeab-334">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="ddeab-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ddeab-335">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-335">Resource filter examples:</span></span>

* <span data-ttu-id="ddeab-336">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ddeab-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ddeab-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ddeab-338">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="ddeab-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ddeab-339">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="ddeab-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ddeab-340">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-340">Action filters</span></span>

<span data-ttu-id="ddeab-341">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="ddeab-342">Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ddeab-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ddeab-343">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ddeab-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ddeab-344">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-344">Action filters:</span></span>

* <span data-ttu-id="ddeab-345">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ddeab-346">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ddeab-347">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddeab-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ddeab-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ddeab-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="ddeab-350"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ddeab-351"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ddeab-352">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ddeab-353">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ddeab-354">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ddeab-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddeab-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ddeab-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ddeab-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddeab-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ddeab-358">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="ddeab-358">Setting this property to null:</span></span>

  * <span data-ttu-id="ddeab-359">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ddeab-360">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ddeab-361">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="ddeab-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ddeab-362">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ddeab-363">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ddeab-364">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="ddeab-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ddeab-365">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ddeab-366">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="ddeab-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ddeab-367">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-367">Validate model state.</span></span>
* <span data-ttu-id="ddeab-368">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="ddeab-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="ddeab-369">Los controladores anotados con el `[ApiController]` atributo validan automáticamente el estado del modelo y devuelven una respuesta 400.</span><span class="sxs-lookup"><span data-stu-id="ddeab-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="ddeab-370">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="ddeab-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="ddeab-371">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ddeab-372">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ddeab-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddeab-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ddeab-375">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ddeab-376">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ddeab-377">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ddeab-378">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-378">Exception filters</span></span>

<span data-ttu-id="ddeab-379">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-379">Exception filters:</span></span>

* <span data-ttu-id="ddeab-380">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="ddeab-381">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ddeab-382">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ddeab-383">El código siguiente prueba el filtro de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="ddeab-384">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-384">Exception filters:</span></span>

* <span data-ttu-id="ddeab-385">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-385">Don't have before and after events.</span></span>
* <span data-ttu-id="ddeab-386">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ddeab-387">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ddeab-388">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="ddeab-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ddeab-389">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="ddeab-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ddeab-390">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-390">This stops propagation of the exception.</span></span> <span data-ttu-id="ddeab-391">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="ddeab-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ddeab-392">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-392">Only an action filter can do that.</span></span>

<span data-ttu-id="ddeab-393">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-393">Exception filters:</span></span>

* <span data-ttu-id="ddeab-394">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ddeab-395">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ddeab-396">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ddeab-397">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="ddeab-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ddeab-398">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="ddeab-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ddeab-399">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="ddeab-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ddeab-400">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="ddeab-400">Result filters</span></span>

<span data-ttu-id="ddeab-401">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="ddeab-401">Result filters:</span></span>

* <span data-ttu-id="ddeab-402">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="ddeab-402">Implement an interface:</span></span>
  * <span data-ttu-id="ddeab-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ddeab-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ddeab-405">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ddeab-406">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddeab-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ddeab-407">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddeab-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddeab-408">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ddeab-409">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="ddeab-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ddeab-410">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ddeab-411">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ddeab-412">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ddeab-413">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="ddeab-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="ddeab-414">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ddeab-415">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ddeab-416">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ddeab-417">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="ddeab-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ddeab-418">Generación de una excepción en `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="ddeab-419">Impide la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ddeab-420">Se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ddeab-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddeab-421">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="ddeab-422">En este caso, no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="ddeab-423">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ddeab-424">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ddeab-425">Establecer `Exception` en un valor null hace que una excepción se controle de forma eficaz y evita que se vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="ddeab-426">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ddeab-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ddeab-427">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="ddeab-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ddeab-428">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ddeab-429">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ddeab-430">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ddeab-431">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ddeab-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ddeab-432">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddeab-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ddeab-433">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ddeab-434">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="ddeab-434">This includes action results produced by:</span></span>

* <span data-ttu-id="ddeab-435">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="ddeab-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ddeab-436">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-436">Exception filters.</span></span>

<span data-ttu-id="ddeab-437">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="ddeab-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ddeab-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ddeab-438">IFilterFactory</span></span>

<span data-ttu-id="ddeab-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ddeab-440">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ddeab-441">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ddeab-442">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ddeab-443">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="ddeab-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ddeab-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="ddeab-445">Es una sugerencia de la factoría que la instancia de filtro creada por el generador se puede volver a usar fuera del ámbito de solicitud en el que se creó.</span><span class="sxs-lookup"><span data-stu-id="ddeab-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="ddeab-446">Debe \***no** _ usarse con un filtro que dependa de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ddeab-446">Should \***not** _ be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ddeab-447">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="ddeab-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

<span data-ttu-id="ddeab-448">_ Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-448">_ That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="ddeab-449">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-449">The filter will not be re-requested from the DI container at some later point.</span></span>

[!WARNING]<span data-ttu-id="ddeab-450"> Solo configure `IFilterFactory.IsReusable` para que devuelva `true` si el origen de los filtros es inequívoco, los filtros no tienen estado y pueden usarse de forma segura en varias solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ddeab-450"> Only configure `IFilterFactory.IsReusable` to return `true` if the source of the filters is unambiguous, the filters are stateless, and are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="ddeab-451">Por ejemplo, no devuelva filtros de DI que estén registrados como de ámbito o transitorios si `IFilterFactory.IsReusable` devuelve `true`</span><span class="sxs-lookup"><span data-stu-id="ddeab-451">For instance, do not return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`</span></span>

<span data-ttu-id="ddeab-452">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="ddeab-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ddeab-453">El filtro se aplica en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="ddeab-454">Pruebe el código anterior mediante la ejecución del [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="ddeab-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="ddeab-455">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="ddeab-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ddeab-456">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ddeab-457">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ddeab-458">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="ddeab-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="ddeab-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ddeab-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ddeab-460">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ddeab-460">**internal:** `My header`</span></span>

<span data-ttu-id="ddeab-461">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ddeab-462">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="ddeab-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ddeab-463">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="ddeab-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ddeab-464">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="ddeab-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="ddeab-465">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ddeab-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddeab-467">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddeab-468">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="ddeab-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ddeab-469">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ddeab-470">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ddeab-471">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ddeab-472">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ddeab-473">Pero los filtros se diferencian del middleware en que forman parte del runtime, lo que significa que tienen acceso al contexto y las construcciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="ddeab-474">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ddeab-475">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="ddeab-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ddeab-476">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="ddeab-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ddeab-477">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ddeab-478">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-478">Next actions</span></span>

* <span data-ttu-id="ddeab-479">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ddeab-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ddeab-480">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="ddeab-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ddeab-481">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ddeab-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ddeab-482">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ddeab-483">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="ddeab-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ddeab-484">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="ddeab-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ddeab-485">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="ddeab-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ddeab-486">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ddeab-487">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ddeab-488">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="ddeab-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="ddeab-489">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ddeab-490">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="ddeab-491">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ddeab-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ddeab-492">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="ddeab-492">How filters work</span></span>

<span data-ttu-id="ddeab-493">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="ddeab-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="ddeab-494">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases de otro middleware, del middleware de enrutamiento, de la selección de acción y de la canalización de invocación de acciones de ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ddeab-497">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-497">Filter types</span></span>

<span data-ttu-id="ddeab-498">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ddeab-499">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ddeab-500">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="ddeab-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="ddeab-501">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ddeab-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ddeab-502">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-502">Run after authorization.</span></span>  
  * <span data-ttu-id="ddeab-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> puede ejecutar código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ddeab-504">Por ejemplo, `OnResourceExecuting` puede ejecutar código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="ddeab-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> puede ejecutar el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ddeab-506">Los [filtros de acciones](#action-filters) pueden ejecutar código inmediatamente antes y después de llamar a un método de acción individual.</span><span class="sxs-lookup"><span data-stu-id="ddeab-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="ddeab-507">Se pueden usar para manipular los argumentos pasados a una acción y el resultado obtenido de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="ddeab-508">**No** se admiten filtros de acción en Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ddeab-509">Los [filtros de excepciones](#exception-filters) sirven para aplicar directivas globales a las excepciones no controladas que se producen antes de que se escriba algo en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ddeab-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="ddeab-510">Los [filtros de resultados](#result-filters) pueden ejecutar código inmediatamente antes y después de la ejecución de resultados de acción individuales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="ddeab-511">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ddeab-512">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ddeab-513">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ddeab-516">Implementación</span><span class="sxs-lookup"><span data-stu-id="ddeab-516">Implementation</span></span>

<span data-ttu-id="ddeab-517">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="ddeab-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ddeab-518">Los filtros sincrónicos pueden ejecutar código antes (`On-Stage-Executing`) y después (`On-Stage-Executed`) de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="ddeab-519">Por ejemplo, `OnActionExecuting` se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="ddeab-520">`OnActionExecuted` se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddeab-521">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ddeab-522">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="ddeab-523">Cada uno de los métodos `On-Stage-ExecutionAsync` toman un `FilterType-ExecutionDelegate` que ejecuta la fase de canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ddeab-524">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-524">Multiple filter stages</span></span>

<span data-ttu-id="ddeab-525">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ddeab-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ddeab-526">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` y sus equivalentes asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="ddeab-527">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ddeab-528">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="ddeab-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ddeab-529">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ddeab-530">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="ddeab-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ddeab-531">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ddeab-532">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="ddeab-532">Built-in filter attributes</span></span>

<span data-ttu-id="ddeab-533">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ddeab-534">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ddeab-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-535">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ddeab-536">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddeab-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="ddeab-537">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ddeab-538">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ddeab-539">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="ddeab-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="ddeab-540">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="ddeab-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ddeab-541">Mediante un atributo en una acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="ddeab-542">Mediante un atributo en un controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="ddeab-543">Globalmente para todos los controladores y las acciones, tal como se muestra en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ddeab-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ddeab-544">El código anterior agrega tres filtros globalmente mediante la colección [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="ddeab-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="ddeab-545">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="ddeab-545">Default order of execution</span></span>

<span data-ttu-id="ddeab-546">Cuando hay varios filtros *del mismo tipo*, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ddeab-547">Los filtros globales delimitan los filtros de clase.</span><span class="sxs-lookup"><span data-stu-id="ddeab-547">Global filters surround class filters.</span></span> <span data-ttu-id="ddeab-548">Los filtros de clase delimitan los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="ddeab-548">Class filters surround method filters.</span></span>

<span data-ttu-id="ddeab-549">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="ddeab-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ddeab-550">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="ddeab-550">The filter sequence:</span></span>

* <span data-ttu-id="ddeab-551">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ddeab-552">El código *anterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="ddeab-553">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ddeab-554">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ddeab-555">El código *posterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="ddeab-556">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ddeab-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ddeab-557">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ddeab-558">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ddeab-558">Sequence</span></span> | <span data-ttu-id="ddeab-559">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-559">Filter scope</span></span> | <span data-ttu-id="ddeab-560">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ddeab-561">1</span><span class="sxs-lookup"><span data-stu-id="ddeab-561">1</span></span> | <span data-ttu-id="ddeab-562">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-563">2</span><span class="sxs-lookup"><span data-stu-id="ddeab-563">2</span></span> | <span data-ttu-id="ddeab-564">Controller</span><span class="sxs-lookup"><span data-stu-id="ddeab-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-565">3</span><span class="sxs-lookup"><span data-stu-id="ddeab-565">3</span></span> | <span data-ttu-id="ddeab-566">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-567">4</span><span class="sxs-lookup"><span data-stu-id="ddeab-567">4</span></span> | <span data-ttu-id="ddeab-568">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddeab-569">5</span><span class="sxs-lookup"><span data-stu-id="ddeab-569">5</span></span> | <span data-ttu-id="ddeab-570">Controller</span><span class="sxs-lookup"><span data-stu-id="ddeab-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ddeab-571">6</span><span class="sxs-lookup"><span data-stu-id="ddeab-571">6</span></span> | <span data-ttu-id="ddeab-572">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="ddeab-573">Esta secuencia pone de manifiesto lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-573">This sequence shows:</span></span>

* <span data-ttu-id="ddeab-574">El filtro de método está anidado en el filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="ddeab-575">El filtro de controlador está anidado en el filtro global.</span><span class="sxs-lookup"><span data-stu-id="ddeab-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="ddeab-576">Filtros de Razor nivel de página y controlador</span><span class="sxs-lookup"><span data-stu-id="ddeab-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="ddeab-577">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ddeab-578">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-578">These methods:</span></span>

* <span data-ttu-id="ddeab-579">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="ddeab-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ddeab-580">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ddeab-581">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ddeab-582">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ddeab-583">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ddeab-584">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="ddeab-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ddeab-585">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-585">The `TestController`:</span></span>

* <span data-ttu-id="ddeab-586">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ddeab-587">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ddeab-588">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ddeab-589">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ddeab-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ddeab-590">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="ddeab-590">Overriding the default order</span></span>

<span data-ttu-id="ddeab-591">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ddeab-592"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ddeab-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ddeab-593">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="ddeab-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ddeab-594">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ddeab-595">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ddeab-596">La propiedad `Order` se puede establecer con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="ddeab-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="ddeab-597">Considere los mismos tres filtros de acción que se muestran en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="ddeab-598">Si la propiedad `Order` del controlador y de los filtros globales está establecida en 1 y 2 respectivamente, el orden de ejecución se invierte.</span><span class="sxs-lookup"><span data-stu-id="ddeab-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="ddeab-599">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ddeab-599">Sequence</span></span> | <span data-ttu-id="ddeab-600">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-600">Filter scope</span></span> | <span data-ttu-id="ddeab-601">Propiedad`Order`</span><span class="sxs-lookup"><span data-stu-id="ddeab-601">`Order` property</span></span> | <span data-ttu-id="ddeab-602">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="ddeab-603">1</span><span class="sxs-lookup"><span data-stu-id="ddeab-603">1</span></span> | <span data-ttu-id="ddeab-604">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-604">Method</span></span> | <span data-ttu-id="ddeab-605">0</span><span class="sxs-lookup"><span data-stu-id="ddeab-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ddeab-606">2</span><span class="sxs-lookup"><span data-stu-id="ddeab-606">2</span></span> | <span data-ttu-id="ddeab-607">Controller</span><span class="sxs-lookup"><span data-stu-id="ddeab-607">Controller</span></span> | <span data-ttu-id="ddeab-608">1</span><span class="sxs-lookup"><span data-stu-id="ddeab-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ddeab-609">3</span><span class="sxs-lookup"><span data-stu-id="ddeab-609">3</span></span> | <span data-ttu-id="ddeab-610">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-610">Global</span></span> | <span data-ttu-id="ddeab-611">2</span><span class="sxs-lookup"><span data-stu-id="ddeab-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ddeab-612">4</span><span class="sxs-lookup"><span data-stu-id="ddeab-612">4</span></span> | <span data-ttu-id="ddeab-613">Global</span><span class="sxs-lookup"><span data-stu-id="ddeab-613">Global</span></span> | <span data-ttu-id="ddeab-614">2</span><span class="sxs-lookup"><span data-stu-id="ddeab-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ddeab-615">5</span><span class="sxs-lookup"><span data-stu-id="ddeab-615">5</span></span> | <span data-ttu-id="ddeab-616">Controller</span><span class="sxs-lookup"><span data-stu-id="ddeab-616">Controller</span></span> | <span data-ttu-id="ddeab-617">1</span><span class="sxs-lookup"><span data-stu-id="ddeab-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ddeab-618">6</span><span class="sxs-lookup"><span data-stu-id="ddeab-618">6</span></span> | <span data-ttu-id="ddeab-619">Método</span><span class="sxs-lookup"><span data-stu-id="ddeab-619">Method</span></span> | <span data-ttu-id="ddeab-620">0</span><span class="sxs-lookup"><span data-stu-id="ddeab-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="ddeab-621">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="ddeab-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ddeab-622">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="ddeab-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ddeab-623">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="ddeab-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ddeab-624">En los filtros integrados, el ámbito determina el orden, a menos que `Order` se establezca en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="ddeab-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ddeab-625">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="ddeab-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ddeab-626">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ddeab-627">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="ddeab-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-628">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ddeab-629">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ddeab-630">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ddeab-631">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ddeab-632">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ddeab-633">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="ddeab-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ddeab-634">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="ddeab-635">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ddeab-635">Dependency injection</span></span>

<span data-ttu-id="ddeab-636">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="ddeab-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ddeab-637">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ddeab-638">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ddeab-639">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="ddeab-639">A type-activated filter means:</span></span>

* <span data-ttu-id="ddeab-640">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="ddeab-640">An instance is created for each request.</span></span>
* <span data-ttu-id="ddeab-641">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ddeab-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ddeab-642">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ddeab-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ddeab-643">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="ddeab-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ddeab-644">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="ddeab-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ddeab-645">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ddeab-646">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ddeab-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ddeab-648">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ddeab-649">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-649">Loggers are available from DI.</span></span> <span data-ttu-id="ddeab-650">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ddeab-651">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ddeab-652">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="ddeab-652">Logging added to filters:</span></span>

* <span data-ttu-id="ddeab-653">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ddeab-654">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ddeab-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ddeab-655">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ddeab-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ddeab-656">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddeab-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="ddeab-657">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ddeab-658"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ddeab-659">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddeab-660">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="ddeab-661">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ddeab-662">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddeab-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ddeab-663">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ddeab-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddeab-664">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="ddeab-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ddeab-665">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ddeab-666">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ddeab-667">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ddeab-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ddeab-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddeab-669">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddeab-670">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ddeab-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ddeab-671">TypeFilterAttribute</span></span>

<span data-ttu-id="ddeab-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="ddeab-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ddeab-673">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ddeab-674">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ddeab-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ddeab-675">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ddeab-676">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ddeab-677">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="ddeab-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ddeab-678">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ddeab-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ddeab-679">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ddeab-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ddeab-680">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ddeab-681">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ddeab-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ddeab-682">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ddeab-683">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="ddeab-683">Authorization filters</span></span>

<span data-ttu-id="ddeab-684">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ddeab-684">Authorization filters:</span></span>

* <span data-ttu-id="ddeab-685">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ddeab-686">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-686">Control access to action methods.</span></span>
* <span data-ttu-id="ddeab-687">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="ddeab-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="ddeab-688">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ddeab-689">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ddeab-690">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="ddeab-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="ddeab-691">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="ddeab-691">Calls the authorization system.</span></span>
* <span data-ttu-id="ddeab-692">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-692">Does not authorize requests.</span></span>

<span data-ttu-id="ddeab-693">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ddeab-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ddeab-694">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="ddeab-694">The exception will not be handled.</span></span>
* <span data-ttu-id="ddeab-695">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ddeab-696">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ddeab-697">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ddeab-698">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="ddeab-698">Resource filters</span></span>

<span data-ttu-id="ddeab-699">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-699">Resource filters:</span></span>

* <span data-ttu-id="ddeab-700">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ddeab-701">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ddeab-702">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="ddeab-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ddeab-703">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ddeab-704">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="ddeab-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ddeab-705">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ddeab-705">Resource filter examples:</span></span>

* <span data-ttu-id="ddeab-706">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ddeab-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ddeab-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ddeab-708">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="ddeab-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ddeab-709">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="ddeab-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ddeab-710">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ddeab-711">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ddeab-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="ddeab-712">Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ddeab-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ddeab-713">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ddeab-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ddeab-714">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-714">Action filters:</span></span>

* <span data-ttu-id="ddeab-715">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ddeab-716">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ddeab-717">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ddeab-718"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ddeab-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ddeab-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="ddeab-720"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="ddeab-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ddeab-721"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ddeab-722">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ddeab-723">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ddeab-724">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ddeab-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddeab-725"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ddeab-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ddeab-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddeab-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ddeab-728">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="ddeab-728">Setting this property to null:</span></span>

  * <span data-ttu-id="ddeab-729">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ddeab-730">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ddeab-731">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="ddeab-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ddeab-732">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ddeab-733">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ddeab-734">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="ddeab-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ddeab-735">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ddeab-736">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="ddeab-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ddeab-737">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="ddeab-737">Validate model state.</span></span>
* <span data-ttu-id="ddeab-738">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="ddeab-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="ddeab-739">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="ddeab-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ddeab-740">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ddeab-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ddeab-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ddeab-743">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ddeab-744">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ddeab-745">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ddeab-746">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-746">Exception filters</span></span>

<span data-ttu-id="ddeab-747">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-747">Exception filters:</span></span>

* <span data-ttu-id="ddeab-748">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="ddeab-749">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ddeab-750">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ddeab-751">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-751">Exception filters:</span></span>

* <span data-ttu-id="ddeab-752">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-752">Don't have before and after events.</span></span>
* <span data-ttu-id="ddeab-753">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ddeab-754">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ddeab-755">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="ddeab-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ddeab-756">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="ddeab-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ddeab-757">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-757">This stops propagation of the exception.</span></span> <span data-ttu-id="ddeab-758">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="ddeab-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ddeab-759">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-759">Only an action filter can do that.</span></span>

<span data-ttu-id="ddeab-760">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ddeab-760">Exception filters:</span></span>

* <span data-ttu-id="ddeab-761">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ddeab-762">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="ddeab-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ddeab-763">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ddeab-764">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="ddeab-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ddeab-765">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="ddeab-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ddeab-766">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="ddeab-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ddeab-767">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="ddeab-767">Result filters</span></span>

<span data-ttu-id="ddeab-768">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="ddeab-768">Result filters:</span></span>

* <span data-ttu-id="ddeab-769">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="ddeab-769">Implement an interface:</span></span>
  * <span data-ttu-id="ddeab-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ddeab-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ddeab-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ddeab-772">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ddeab-773">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddeab-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ddeab-774">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ddeab-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ddeab-775">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ddeab-776">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="ddeab-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ddeab-777">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="ddeab-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ddeab-778">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="ddeab-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ddeab-779">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ddeab-780">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="ddeab-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="ddeab-781">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ddeab-782">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ddeab-783">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ddeab-784">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="ddeab-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ddeab-785">Si se inicia una excepción en `IResultFilter.OnResultExecuting`, sucederá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ddeab-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="ddeab-786">Se impedirá la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ddeab-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ddeab-787">Se tratará como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ddeab-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ddeab-788">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="ddeab-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="ddeab-789">En este caso, no se puede cambiar más.</span><span class="sxs-lookup"><span data-stu-id="ddeab-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="ddeab-790">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ddeab-791">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ddeab-792">Establecer `Exception` en un valor NULL hace que una excepción se "controle" de forma eficaz y evita que ASP.NET Core vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="ddeab-793">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ddeab-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ddeab-794">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="ddeab-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ddeab-795">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ddeab-796">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ddeab-797">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ddeab-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ddeab-798">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ddeab-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ddeab-799">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ddeab-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ddeab-800">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ddeab-801">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="ddeab-801">This includes action results produced by:</span></span>

* <span data-ttu-id="ddeab-802">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="ddeab-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ddeab-803">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="ddeab-803">Exception filters.</span></span>

<span data-ttu-id="ddeab-804">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="ddeab-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ddeab-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ddeab-805">IFilterFactory</span></span>

<span data-ttu-id="ddeab-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ddeab-807">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ddeab-808">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ddeab-809">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="ddeab-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ddeab-810">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="ddeab-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ddeab-811">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="ddeab-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ddeab-812">El código anterior se puede probar mediante la ejecución del [ejemplo de descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="ddeab-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="ddeab-813">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="ddeab-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ddeab-814">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ddeab-815">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ddeab-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ddeab-816">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="ddeab-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="ddeab-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ddeab-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ddeab-818">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ddeab-818">**internal:** `My header`</span></span>

<span data-ttu-id="ddeab-819">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ddeab-820">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="ddeab-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ddeab-821">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="ddeab-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ddeab-822">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="ddeab-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="ddeab-823">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ddeab-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ddeab-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ddeab-825">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ddeab-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ddeab-826">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="ddeab-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ddeab-827">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="ddeab-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ddeab-828">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ddeab-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ddeab-829">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="ddeab-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ddeab-830">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ddeab-831">Pero los filtros se diferencian del middleware en que forman parte del entorno de ejecución de ASP.NET Core, lo que significa que tienen acceso al contexto y las construcciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ddeab-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="ddeab-832">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ddeab-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ddeab-833">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="ddeab-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ddeab-834">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="ddeab-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ddeab-835">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ddeab-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ddeab-836">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="ddeab-836">Next actions</span></span>

* <span data-ttu-id="ddeab-837">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ddeab-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ddeab-838">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="ddeab-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
