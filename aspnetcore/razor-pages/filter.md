---
title: 'Métodos de filtrado de :::no-loc(Razor)::: Pages de ASP.NET Core'
author: Rick-Anderson
description: 'Aprenda a crear métodos de filtrado de :::no-loc(Razor)::: Pages de ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056834"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="34a38-103">Métodos de filtrado de :::no-loc(Razor)::: Pages de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34a38-103">Filter methods for :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34a38-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34a38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="34a38-105">Los filtros de páginas de :::no-loc(Razor)::: [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permiten que las instancias de :::no-loc(Razor)::: Pages ejecuten código antes y después de que se haya ejecutado el controlador de una página de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="34a38-105">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="34a38-106">Los filtros de las páginas de :::no-loc(Razor)::: son similares a los [filtros de acciones de ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), salvo por el hecho de que no se pueden aplicar a métodos de control de páginas individuales.</span><span class="sxs-lookup"><span data-stu-id="34a38-106">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="34a38-107">Los filtros de páginas de :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="34a38-107">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="34a38-108">Ejecutan código después de que se haya seleccionado un método de controlador, pero antes de que el enlace de modelos tenga lugar.</span><span class="sxs-lookup"><span data-stu-id="34a38-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="34a38-109">Ejecutan código antes de que se ejecute el método de controlador, después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="34a38-110">Ejecutan código después de que se haya ejecutado el método de controlador.</span><span class="sxs-lookup"><span data-stu-id="34a38-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="34a38-111">Se pueden implementar en una página o globalmente.</span><span class="sxs-lookup"><span data-stu-id="34a38-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="34a38-112">No se pueden usar con métodos de controlador de páginas específicas.</span><span class="sxs-lookup"><span data-stu-id="34a38-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="34a38-113">Pueden tener dependencias de constructor rellenadas mediante la [Inserción de dependencias](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="34a38-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="34a38-114">Para obtener más información, vea [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) y [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="34a38-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="34a38-115">Mientras que los constructores de páginas y el middleware permiten la ejecución de código personalizado antes de que se ejecute un método de control, solo los filtros de páginas de :::no-loc(Razor)::: permiten el acceso a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> y a la página.</span><span class="sxs-lookup"><span data-stu-id="34a38-115">While page constructors and middleware enable executing custom code before a handler method executes, only :::no-loc(Razor)::: Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="34a38-116">El middleware tiene acceso a `HttpContext`, pero no al "contexto de la página".</span><span class="sxs-lookup"><span data-stu-id="34a38-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="34a38-117">Los filtros tienen un parámetro derivado <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> que proporciona acceso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="34a38-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="34a38-118">A continuación, se muestra un ejemplo de un filtro de página: [Implemente un atributo de filtro](#ifa) que agregue un encabezado a la respuesta, lo cual que no es posible con constructores o con middleware.</span><span class="sxs-lookup"><span data-stu-id="34a38-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="34a38-119">El acceso al contexto de la página, que incluye el acceso a las instancias de la página y a su modelo, solo está disponible cuando se ejecutan filtros, controladores o el cuerpo de una página de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="34a38-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="34a38-120">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34a38-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="34a38-121">Los filtros de páginas de :::no-loc(Razor)::: proporcionan los siguientes métodos, que se pueden aplicar globalmente o bien en el nivel de página:</span><span class="sxs-lookup"><span data-stu-id="34a38-121">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="34a38-122">Métodos sincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-122">Synchronous methods:</span></span>

  * <span data-ttu-id="34a38-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): se llama a este método después de que se haya seleccionado un método de controlador, pero antes de que se produzca el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="34a38-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="34a38-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): se llama a este método antes de que se ejecute el método de controlador, pero después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="34a38-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): se llama a este método después de que se ejecute el método de controlador, pero antes de obtener el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="34a38-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="34a38-126">Métodos asincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="34a38-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): se llama a este método de forma asincrónica después de que se haya seleccionado un método de controlador, pero antes de que se produzca el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="34a38-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="34a38-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): se llama a este método de forma asincrónica antes de que se invoque el método de controlador, pero después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="34a38-129">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="34a38-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="34a38-130">El marco comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, es a la interfaz que llama.</span><span class="sxs-lookup"><span data-stu-id="34a38-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="34a38-131">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="34a38-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="34a38-132">Si se implementan ambas interfaces, solo se llamará a los métodos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="34a38-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="34a38-133">La misma regla se cumple con las invalidaciones en páginas: implemente la versión sincrónica o asincrónica de la invalidación, pero no ambas.</span><span class="sxs-lookup"><span data-stu-id="34a38-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="34a38-134">Implementación de filtros de páginas de :::no-loc(Razor)::: globalmente</span><span class="sxs-lookup"><span data-stu-id="34a38-134">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="34a38-135">El siguiente código implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="34a38-136">En el código anterior, `ProcessUserAgent.Write` es código proporcionado por el usuario que funciona con la cadena del agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="34a38-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="34a38-137">El siguiente código habilita `SampleAsyncPageFilter` en la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="34a38-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="34a38-138">El código siguiente llama a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para aplicar `SampleAsyncPageFilter` solo a las páginas en */Movies* :</span><span class="sxs-lookup"><span data-stu-id="34a38-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="34a38-139">El siguiente código implementa el método sincrónico `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="34a38-140">El siguiente código habilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="34a38-141">Implementación de filtros de páginas de :::no-loc(Razor)::: mediante la invalidación de métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="34a38-141">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="34a38-142">El código siguiente invalida los filtros de páginas de :::no-loc(Razor)::: asincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-142">The following code overrides the asynchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="34a38-143">Implementar un atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="34a38-143">Implement a filter attribute</span></span>

<span data-ttu-id="34a38-144">Se pueden crear subclases del filtro <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> basado en atributos integrados.</span><span class="sxs-lookup"><span data-stu-id="34a38-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="34a38-145">El siguiente filtro agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="34a38-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="34a38-146">El siguiente código se aplica al atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="34a38-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="34a38-147">Para examinar los encabezados, use una herramienta como, por ejemplo, las herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="34a38-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="34a38-148">En **Encabezados de respuesta** , se muestra `author: Rick`.</span><span class="sxs-lookup"><span data-stu-id="34a38-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="34a38-149">Vea [Invalidación del orden predeterminado](xref:mvc/controllers/filters#overriding-the-default-order) para obtener instrucciones sobre cómo invalidar el orden.</span><span class="sxs-lookup"><span data-stu-id="34a38-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="34a38-150">Vea [Cancelación y cortocircuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obtener instrucciones sobre cómo cortocircuitar la canalización de filtro de un filtro.</span><span class="sxs-lookup"><span data-stu-id="34a38-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="34a38-151">Atributo de filtro Authorize</span><span class="sxs-lookup"><span data-stu-id="34a38-151">Authorize filter attribute</span></span>

<span data-ttu-id="34a38-152">El atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) se puede aplicar a un `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="34a38-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34a38-153">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34a38-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="34a38-154">Los filtros de páginas de :::no-loc(Razor)::: [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permiten que las instancias de :::no-loc(Razor)::: Pages ejecuten código antes y después de que se haya ejecutado el controlador de una página de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="34a38-154">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="34a38-155">Los filtros de las páginas de :::no-loc(Razor)::: son similares a los [filtros de acciones de ASP.NET Core MVC](xref:mvc/controllers/filters#action-filters), salvo por el hecho de que no se pueden aplicar a métodos de control de páginas individuales.</span><span class="sxs-lookup"><span data-stu-id="34a38-155">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="34a38-156">Los filtros de páginas de :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="34a38-156">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="34a38-157">Ejecutan código después de que se haya seleccionado un método de controlador, pero antes de que el enlace de modelos tenga lugar.</span><span class="sxs-lookup"><span data-stu-id="34a38-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="34a38-158">Ejecutan código antes de que se ejecute el método de controlador, después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="34a38-159">Ejecutan código después de que se haya ejecutado el método de controlador.</span><span class="sxs-lookup"><span data-stu-id="34a38-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="34a38-160">Se pueden implementar en una página o globalmente.</span><span class="sxs-lookup"><span data-stu-id="34a38-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="34a38-161">No se pueden usar con métodos de controlador de páginas específicas.</span><span class="sxs-lookup"><span data-stu-id="34a38-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="34a38-162">Se puede ejecutar código antes de que un método de control se ejecute por medio del constructor de página o el middleware, pero solo los filtros de páginas de :::no-loc(Razor)::: tienen acceso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="34a38-162">Code can be run before a handler method executes using the page constructor or middleware, but only :::no-loc(Razor)::: Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span></span> <span data-ttu-id="34a38-163">Los filtros tienen un parámetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) que concede acceso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="34a38-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="34a38-164">Por ejemplo, en el ejemplo [Implementar un atributo de filtro](#ifa) se agrega un encabezado a la respuesta, cosa que no es posible con constructores o con middleware.</span><span class="sxs-lookup"><span data-stu-id="34a38-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="34a38-165">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34a38-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="34a38-166">Los filtros de páginas de :::no-loc(Razor)::: proporcionan los siguientes métodos, que se pueden aplicar globalmente o bien en el nivel de página:</span><span class="sxs-lookup"><span data-stu-id="34a38-166">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="34a38-167">Métodos sincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-167">Synchronous methods:</span></span>

  * <span data-ttu-id="34a38-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): se llama a este método después de que se haya seleccionado un método de controlador, pero antes de que se produzca el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="34a38-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="34a38-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): se llama a este método antes de que se ejecute el método de controlador, pero después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="34a38-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): se llama a este método después de que se ejecute el método de controlador, pero antes de obtener el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="34a38-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="34a38-171">Métodos asincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="34a38-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): se llama a este método de forma asincrónica después de que se haya seleccionado un método de controlador, pero antes de que se produzca el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="34a38-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="34a38-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): se llama a este método de forma asincrónica antes de que se invoque el método de controlador, después de que el enlace de modelos se haya completado.</span><span class="sxs-lookup"><span data-stu-id="34a38-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="34a38-174">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero no ambas.</span><span class="sxs-lookup"><span data-stu-id="34a38-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="34a38-175">El marco comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, es a la interfaz que llama.</span><span class="sxs-lookup"><span data-stu-id="34a38-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="34a38-176">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="34a38-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="34a38-177">Si se implementan ambas interfaces, solo se llamará a los métodos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="34a38-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="34a38-178">La misma regla se cumple con las invalidaciones en páginas: implemente la versión sincrónica o asincrónica de la invalidación, pero no ambas.</span><span class="sxs-lookup"><span data-stu-id="34a38-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="34a38-179">Implementación de filtros de páginas de :::no-loc(Razor)::: globalmente</span><span class="sxs-lookup"><span data-stu-id="34a38-179">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="34a38-180">El siguiente código implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="34a38-181">En el código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) no es necesario;</span><span class="sxs-lookup"><span data-stu-id="34a38-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="34a38-182">se usa para proporcionar información de seguimiento relativa a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="34a38-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="34a38-183">El siguiente código habilita `SampleAsyncPageFilter` en la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="34a38-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="34a38-184">El siguiente código muestra la clase `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="34a38-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="34a38-185">El siguiente código llama a `AddFolderApplicationModelConvention` para aplicar `SampleAsyncPageFilter` solo a las páginas en */subFolder* :</span><span class="sxs-lookup"><span data-stu-id="34a38-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="34a38-186">El siguiente código implementa el método sincrónico `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="34a38-187">El siguiente código habilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="34a38-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="34a38-188">Implementación de filtros de páginas de :::no-loc(Razor)::: mediante la invalidación de métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="34a38-188">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="34a38-189">El código siguiente invalida los filtros de páginas de :::no-loc(Razor)::: sincrónicos:</span><span class="sxs-lookup"><span data-stu-id="34a38-189">The following code overrides the synchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="34a38-190">Implementar un atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="34a38-190">Implement a filter attribute</span></span>

<span data-ttu-id="34a38-191">El filtro basado en atributos integrado [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) puede ser una subclase.</span><span class="sxs-lookup"><span data-stu-id="34a38-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="34a38-192">El siguiente filtro agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="34a38-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="34a38-193">El siguiente código se aplica al atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="34a38-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="34a38-194">Vea [Invalidación del orden predeterminado](xref:mvc/controllers/filters#overriding-the-default-order) para obtener instrucciones sobre cómo invalidar el orden.</span><span class="sxs-lookup"><span data-stu-id="34a38-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="34a38-195">Vea [Cancelación y cortocircuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obtener instrucciones sobre cómo cortocircuitar la canalización de filtro de un filtro.</span><span class="sxs-lookup"><span data-stu-id="34a38-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="34a38-196">Atributo de filtro Authorize</span><span class="sxs-lookup"><span data-stu-id="34a38-196">Authorize filter attribute</span></span>

<span data-ttu-id="34a38-197">El atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) se puede aplicar a un `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="34a38-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end