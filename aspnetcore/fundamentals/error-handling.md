---
title: Controlar errores en ASP.NET Core
author: rick-anderson
description: Descubra cómo controlar errores en aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: 1687195bc5d80d50289db51a5e8ac6113ca8b05f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586129"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="f71e0-103">Controlar errores en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f71e0-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f71e0-104">De [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f71e0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f71e0-105">Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f71e0-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f71e0-106">Consulte <xref:web-api/handle-errors> para las API web.</span><span class="sxs-lookup"><span data-stu-id="f71e0-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f71e0-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f71e0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f71e0-108">([Cómo descargarlo](xref:index#how-to-download-a-sample)). La pestaña Red de las herramientas de desarrollo del explorador F12 resulta útil al probar la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f71e0-109">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="f71e0-109">Developer Exception Page</span></span>

<span data-ttu-id="f71e0-110">En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f71e0-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f71e0-111">Las plantillas de ASP.NET Core generan el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="f71e0-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="f71e0-112">El código resaltado anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f71e0-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f71e0-113">Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> al principio de la canalización de middleware para que pueda detectar las excepciones que se producen en el middleware que sigue.</span><span class="sxs-lookup"><span data-stu-id="f71e0-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="f71e0-114">El código anterior ***solo*** habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="f71e0-115">La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en el entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="f71e0-116">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f71e0-117">La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:</span><span class="sxs-lookup"><span data-stu-id="f71e0-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f71e0-118">Seguimiento de la pila</span><span class="sxs-lookup"><span data-stu-id="f71e0-118">Stack trace</span></span>
* <span data-ttu-id="f71e0-119">Parámetros de cadena de consulta (si existen)</span><span class="sxs-lookup"><span data-stu-id="f71e0-119">Query string parameters if any</span></span>
* <span data-ttu-id="f71e0-120">Cookie (si existen)</span><span class="sxs-lookup"><span data-stu-id="f71e0-120">Cookies if any</span></span>
* <span data-ttu-id="f71e0-121">Encabezados</span><span class="sxs-lookup"><span data-stu-id="f71e0-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f71e0-122">Página del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-122">Exception handler page</span></span>

<span data-ttu-id="f71e0-123">Para configurar una página de control de errores personalizada para el [entorno de producción](xref:fundamentals/environments), llame a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f71e0-124">Este middleware de control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="f71e0-124">This exception handling middleware:</span></span>

* <span data-ttu-id="f71e0-125">Captura y registra las excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f71e0-126">Vuelve a ejecutar la solicitud en una canalización alternativa con la ruta de acceso indicada.</span><span class="sxs-lookup"><span data-stu-id="f71e0-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="f71e0-127">La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f71e0-128">El código generado por la plantilla vuelve a ejecutar la solicitud mediante la ruta de acceso `/Error`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="f71e0-129">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="f71e0-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f71e0-130">La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f71e0-131">Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción de `Error` y una vista del error para el controlador de inicio.</span><span class="sxs-lookup"><span data-stu-id="f71e0-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="f71e0-132">El middleware de control de excepciones vuelve a ejecutar la solicitud mediante el método HTTP *original*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="f71e0-133">Si un punto de conexión de controlador de errores está restringido a un conjunto específico de métodos HTTP, solo se ejecuta para esos métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="f71e0-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="f71e0-134">Por ejemplo, una acción de controlador de MVC que usa el atributo `[HttpGet]` solo se ejecuta para solicitudes GET.</span><span class="sxs-lookup"><span data-stu-id="f71e0-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="f71e0-135">Para asegurarse de que *todas* las solicitudes lleguen a la página de control de errores personalizada, no las restrinja a un conjunto específico de métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="f71e0-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="f71e0-136">Para controlar las excepciones de manera diferente en función del método HTTP original:</span><span class="sxs-lookup"><span data-stu-id="f71e0-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="f71e0-137">En el caso de Razor Pages, cree varios métodos de control.</span><span class="sxs-lookup"><span data-stu-id="f71e0-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="f71e0-138">Por ejemplo, use `OnGet` para controlar las excepciones GET y use `OnPost` para controlar las excepciones POST.</span><span class="sxs-lookup"><span data-stu-id="f71e0-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="f71e0-139">Para MVC, aplique los atributos de verbo HTTP a varias acciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="f71e0-140">Por ejemplo, use `[HttpGet]` para controlar las excepciones GET y use `[HttpPost]` para controlar las excepciones POST.</span><span class="sxs-lookup"><span data-stu-id="f71e0-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="f71e0-141">Para permitir que los usuarios no autenticados vean la página de control de errores personalizada, asegúrese de que admite el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f71e0-142">Acceso a la excepción</span><span class="sxs-lookup"><span data-stu-id="f71e0-142">Access the exception</span></span>

<span data-ttu-id="f71e0-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso de la solicitud original y a la excepción en un controlador de errores.</span><span class="sxs-lookup"><span data-stu-id="f71e0-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="f71e0-144">El código siguiente agrega `ExceptionMessage` al archivo *Pages/Error.cshtml.cs* predeterminado que se genera con las plantillas de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f71e0-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="f71e0-145">**No** proporcione información de errores confidencial a los clientes.</span><span class="sxs-lookup"><span data-stu-id="f71e0-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f71e0-146">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="f71e0-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="f71e0-147">Para probar la excepción en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f71e0-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f71e0-148">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-148">Set the environment to production.</span></span>
* <span data-ttu-id="f71e0-149">Quite los comentarios de `webBuilder.UseStartup<Startup>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f71e0-150">Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="f71e0-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f71e0-151">Lambda del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-151">Exception handler lambda</span></span>

<span data-ttu-id="f71e0-152">Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f71e0-153">Usar una expresión lambda permite acceder al error antes de devolver la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f71e0-154">En el código siguiente se usa una expresión lambda para el control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="f71e0-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="f71e0-155">**No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes.</span><span class="sxs-lookup"><span data-stu-id="f71e0-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f71e0-156">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="f71e0-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="f71e0-157">Para probar la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f71e0-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f71e0-158">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-158">Set the environment to production.</span></span>
* <span data-ttu-id="f71e0-159">Quite los comentarios de `webBuilder.UseStartup<StartupLambda>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f71e0-160">Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="f71e0-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f71e0-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f71e0-161">UseStatusCodePages</span></span>

<span data-ttu-id="f71e0-162">Una aplicación ASP.NET Core no proporciona de forma predeterminada ninguna página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f71e0-163">Cuando la aplicación encuentra un código de estado de error HTTP 400-599 que no tiene cuerpo, devuelve el código de estado y un cuerpo de respuesta vacío.</span><span class="sxs-lookup"><span data-stu-id="f71e0-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="f71e0-164">Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="f71e0-165">Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f71e0-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="f71e0-166">Llame a `UseStatusCodePages` antes del middleware de control de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f71e0-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="f71e0-167">Por ejemplo, llame a `UseStatusCodePages` antes del middleware de archivos estáticos y el middleware de puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="f71e0-168">Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f71e0-169">Por ejemplo, al navegar a `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f71e0-170">Cuando se llama a `UseStatusCodePages`, el explorador devuelve:</span><span class="sxs-lookup"><span data-stu-id="f71e0-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="f71e0-171">`UseStatusCodePages` no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="f71e0-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f71e0-172">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="f71e0-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f71e0-173">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-173">Set the environment to production.</span></span>
* <span data-ttu-id="f71e0-174">Quite los comentarios de `webBuilder.UseStartup<StartupUseStatusCodePages>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f71e0-175">Seleccione los vínculos en la página principal.</span><span class="sxs-lookup"><span data-stu-id="f71e0-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="f71e0-176">El middleware de páginas de códigos de estado **no** detecta excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="f71e0-177">Para proporcionar una página de control de errores personalizada, use la [página del controlador de excepciones](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="f71e0-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f71e0-178">UseStatusCodePages con cadena de formato</span><span class="sxs-lookup"><span data-stu-id="f71e0-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f71e0-179">Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:</span><span class="sxs-lookup"><span data-stu-id="f71e0-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="f71e0-180">En el código anterior, `{0}` es un marcador de posición para el código de error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="f71e0-181">`UseStatusCodePages` con una cadena de formato no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="f71e0-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f71e0-182">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupFormat>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f71e0-183">UseStatusCodePages con una expresión lambda</span><span class="sxs-lookup"><span data-stu-id="f71e0-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f71e0-184">Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:</span><span class="sxs-lookup"><span data-stu-id="f71e0-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="f71e0-185">`UseStatusCodePages` con una expresión lambda no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="f71e0-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f71e0-186">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupStatusLambda>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f71e0-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f71e0-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f71e0-188">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f71e0-189">Envía un código de estado [302 - Encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/302) al cliente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="f71e0-190">Redirige el cliente al punto de conexión de control de errores proporcionado en la plantilla de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="f71e0-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="f71e0-191">El punto de conexión de control de errores suele mostrar información de error y devuelve HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f71e0-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="f71e0-192">La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, tal y como se muestra en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="f71e0-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="f71e0-193">Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f71e0-194">Si especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f71e0-195">Para obtener un ejemplo de Razor Pages, consulte [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f71e0-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f71e0-196">Este método se usa normalmente cuando la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f71e0-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f71e0-197">Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f71e0-198">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.</span><span class="sxs-lookup"><span data-stu-id="f71e0-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f71e0-199">No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.</span><span class="sxs-lookup"><span data-stu-id="f71e0-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="f71e0-200">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCredirect>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f71e0-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f71e0-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f71e0-202">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f71e0-203">Devuelve el código de estado original al cliente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f71e0-204">Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.</span><span class="sxs-lookup"><span data-stu-id="f71e0-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="f71e0-205">Si se especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f71e0-206">Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f71e0-207">Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f71e0-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f71e0-208">Este método se usa normalmente cuando la aplicación debe:</span><span class="sxs-lookup"><span data-stu-id="f71e0-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f71e0-209">Procesar la solicitud sin redirigirla a un punto de conexión diferente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f71e0-210">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f71e0-211">Conservar y devolver el código de estado original con la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f71e0-212">Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición `{0}` relativo al código de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="f71e0-213">La plantilla de dirección URL debe empezar con `/`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="f71e0-214">El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f71e0-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="f71e0-215">Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="f71e0-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f71e0-216">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCreX>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="f71e0-217">Deshabilitar las páginas de códigos de estado</span><span class="sxs-lookup"><span data-stu-id="f71e0-217">Disable status code pages</span></span>

<span data-ttu-id="f71e0-218">Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="f71e0-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f71e0-219">Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="f71e0-220">Código de control de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-220">Exception-handling code</span></span>

<span data-ttu-id="f71e0-221">El código de las páginas de control de excepciones también puede iniciar excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="f71e0-222">Las páginas de errores de producción se deben probar minuciosamente y se debe tener especial cuidado para evitar que inicien sus propias excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="f71e0-223">Encabezados de respuesta</span><span class="sxs-lookup"><span data-stu-id="f71e0-223">Response headers</span></span>

<span data-ttu-id="f71e0-224">Una vez enviados los encabezados de una respuesta:</span><span class="sxs-lookup"><span data-stu-id="f71e0-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f71e0-225">La aplicación no puede cambiar el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f71e0-226">No se pueden ejecutar páginas o controladores de excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f71e0-227">Deberá completarse la respuesta o anularse la conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f71e0-228">Control de excepciones del servidor</span><span class="sxs-lookup"><span data-stu-id="f71e0-228">Server exception handling</span></span>

<span data-ttu-id="f71e0-229">Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f71e0-230">Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta `500 - Internal Server Error` sin cuerpo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="f71e0-231">Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f71e0-232">El servidor controla las solicitudes que no controla la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="f71e0-233">El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f71e0-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f71e0-234">Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="f71e0-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f71e0-235">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="f71e0-235">Startup exception handling</span></span>

<span data-ttu-id="f71e0-236">Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f71e0-237">El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f71e0-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f71e0-238">La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host.</span><span class="sxs-lookup"><span data-stu-id="f71e0-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f71e0-239">Si se produce un error de enlace:</span><span class="sxs-lookup"><span data-stu-id="f71e0-239">If binding fails:</span></span>

* <span data-ttu-id="f71e0-240">La capa de hospedaje registra una excepción crítica.</span><span class="sxs-lookup"><span data-stu-id="f71e0-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f71e0-241">El proceso de dotnet se bloquea.</span><span class="sxs-lookup"><span data-stu-id="f71e0-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="f71e0-242">No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f71e0-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f71e0-243">Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="f71e0-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f71e0-244">Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f71e0-245">Página de error de la base de datos</span><span class="sxs-lookup"><span data-stu-id="f71e0-245">Database error page</span></span>

<span data-ttu-id="f71e0-246">El filtro de excepciones `AddDatabaseDeveloperPageExceptionFilter` de la página del desarrollador de bases de datos captura las excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f71e0-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="f71e0-247">Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="f71e0-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="f71e0-248">Esta página debe habilitarse solo en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="f71e0-249">El siguiente código fue generado por las plantillas de Razor Pages de ASP.NET Core cuando se especificaron cuentas de usuario individuales:</span><span class="sxs-lookup"><span data-stu-id="f71e0-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="f71e0-250">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-250">Exception filters</span></span>

<span data-ttu-id="f71e0-251">En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f71e0-252">En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="f71e0-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f71e0-253">Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro.</span><span class="sxs-lookup"><span data-stu-id="f71e0-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f71e0-254">Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="f71e0-255">Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el [middleware de control de excepciones](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) integrado, `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="f71e0-256">Se recomienda usar `UseExceptionHandler`, a no ser que tenga que realizar el control de errores de otra forma según la acción de MVC elegida.</span><span class="sxs-lookup"><span data-stu-id="f71e0-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="f71e0-257">Errores de estado del modelo</span><span class="sxs-lookup"><span data-stu-id="f71e0-257">Model state errors</span></span>

<span data-ttu-id="f71e0-258">Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f71e0-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f71e0-259">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f71e0-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f71e0-260">De [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f71e0-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f71e0-261">Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f71e0-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f71e0-262">Consulte <xref:web-api/handle-errors> para las API web.</span><span class="sxs-lookup"><span data-stu-id="f71e0-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f71e0-263">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f71e0-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f71e0-264">([Cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f71e0-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f71e0-265">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="f71e0-265">Developer Exception Page</span></span>

<span data-ttu-id="f71e0-266">En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f71e0-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f71e0-267">Las plantillas de ASP.NET Core generan el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="f71e0-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="f71e0-268">El código anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f71e0-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f71e0-269">Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> antes que cualquier middleware para que las excepciones se detecten en el middleware que sigue.</span><span class="sxs-lookup"><span data-stu-id="f71e0-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="f71e0-270">El código anterior solo habilita la página de excepciones para el desarrollador **cuando la aplicación se ejecuta en el entorno de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="f71e0-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f71e0-271">La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en producción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="f71e0-272">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f71e0-273">La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:</span><span class="sxs-lookup"><span data-stu-id="f71e0-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f71e0-274">Seguimiento de la pila</span><span class="sxs-lookup"><span data-stu-id="f71e0-274">Stack trace</span></span>
* <span data-ttu-id="f71e0-275">Parámetros de cadena de consulta (si existen)</span><span class="sxs-lookup"><span data-stu-id="f71e0-275">Query string parameters if any</span></span>
* <span data-ttu-id="f71e0-276">Cookie (si existen)</span><span class="sxs-lookup"><span data-stu-id="f71e0-276">Cookies if any</span></span>
* <span data-ttu-id="f71e0-277">Encabezados</span><span class="sxs-lookup"><span data-stu-id="f71e0-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f71e0-278">Página del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-278">Exception handler page</span></span>

<span data-ttu-id="f71e0-279">Para configurar una página de control de errores personalizada para el entorno de producción, use el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="f71e0-280">El middleware:</span><span class="sxs-lookup"><span data-stu-id="f71e0-280">The middleware:</span></span>

* <span data-ttu-id="f71e0-281">Captura y registra las excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f71e0-282">Vuelve a ejecutar la solicitud en una canalización alternativa correspondiente a la página o el controlador indicados.</span><span class="sxs-lookup"><span data-stu-id="f71e0-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="f71e0-283">La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f71e0-284">El código generado por la plantilla vuelve a ejecutar la solicitud para `/Error`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="f71e0-285">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="f71e0-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f71e0-286">La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f71e0-287">Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción para el error y una vista del error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="f71e0-288">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f71e0-289">Los verbos explícitos impiden que algunas solicitudes lleguen al método.</span><span class="sxs-lookup"><span data-stu-id="f71e0-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f71e0-290">Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f71e0-291">Acceso a la excepción</span><span class="sxs-lookup"><span data-stu-id="f71e0-291">Access the exception</span></span>

<span data-ttu-id="f71e0-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso a la solicitud original y a la excepción en una página o un controlador de errores:</span><span class="sxs-lookup"><span data-stu-id="f71e0-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="f71e0-293">**No** proporcione información de errores confidencial a los clientes.</span><span class="sxs-lookup"><span data-stu-id="f71e0-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f71e0-294">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="f71e0-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="f71e0-295">Para desencadenar la página de control de excepciones anterior, establezca el entorno en producciones y fuerce una excepción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f71e0-296">Lambda del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-296">Exception handler lambda</span></span>

<span data-ttu-id="f71e0-297">Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f71e0-298">Usar una expresión lambda permite acceder al error antes de devolver la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f71e0-299">Este es un ejemplo del uso de una expresión lambda para el control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="f71e0-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="f71e0-300">En el código anterior, se agrega `await context.Response.WriteAsync(new string(' ', 512));` para que el explorador Internet Explorer muestre el mensaje de error en lugar de un mensaje de error de IE.</span><span class="sxs-lookup"><span data-stu-id="f71e0-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="f71e0-301">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="f71e0-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="f71e0-302">**No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes.</span><span class="sxs-lookup"><span data-stu-id="f71e0-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f71e0-303">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="f71e0-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="f71e0-304">Para ver el resultado de la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerLambda` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="f71e0-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f71e0-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f71e0-305">UseStatusCodePages</span></span>

<span data-ttu-id="f71e0-306">Una aplicación ASP.NET Core no proporciona de forma predeterminada una página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="f71e0-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f71e0-307">La aplicación devuelve un código de estado y un cuerpo de respuesta vacío.</span><span class="sxs-lookup"><span data-stu-id="f71e0-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="f71e0-308">Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="f71e0-309">El middleware está disponible en el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/).</span><span class="sxs-lookup"><span data-stu-id="f71e0-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="f71e0-310">Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f71e0-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="f71e0-311">Llame a `UseStatusCodePages` antes del middleware de control de solicitudes (por ejemplo, middleware de archivos estáticos y middleware de MVC).</span><span class="sxs-lookup"><span data-stu-id="f71e0-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="f71e0-312">Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f71e0-313">Por ejemplo, al navegar a `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f71e0-314">Cuando se llama a `UseStatusCodePages`, el explorador devuelve:</span><span class="sxs-lookup"><span data-stu-id="f71e0-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f71e0-315">UseStatusCodePages con cadena de formato</span><span class="sxs-lookup"><span data-stu-id="f71e0-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f71e0-316">Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:</span><span class="sxs-lookup"><span data-stu-id="f71e0-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f71e0-317">UseStatusCodePages con una expresión lambda</span><span class="sxs-lookup"><span data-stu-id="f71e0-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f71e0-318">Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:</span><span class="sxs-lookup"><span data-stu-id="f71e0-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f71e0-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f71e0-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f71e0-320">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f71e0-321">Envía un código de estado *302 - Encontrado* al cliente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="f71e0-322">Redirige al cliente a la ubicación proporcionada en la plantilla de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="f71e0-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="f71e0-323">La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, como se muestra en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="f71e0-324">Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f71e0-325">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f71e0-326">Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f71e0-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f71e0-327">Este método se usa normalmente cuando la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f71e0-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f71e0-328">Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f71e0-329">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.</span><span class="sxs-lookup"><span data-stu-id="f71e0-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f71e0-330">No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.</span><span class="sxs-lookup"><span data-stu-id="f71e0-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f71e0-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f71e0-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f71e0-332">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f71e0-333">Devuelve el código de estado original al cliente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f71e0-334">Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.</span><span class="sxs-lookup"><span data-stu-id="f71e0-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="f71e0-335">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f71e0-336">Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f71e0-337">Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f71e0-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f71e0-338">Este método se usa normalmente cuando la aplicación debe:</span><span class="sxs-lookup"><span data-stu-id="f71e0-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f71e0-339">Procesar la solicitud sin redirigirla a un punto de conexión diferente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f71e0-340">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="f71e0-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f71e0-341">Conservar y devolver el código de estado original con la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f71e0-342">Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición (`{0}`) relativo al código de estado.</span><span class="sxs-lookup"><span data-stu-id="f71e0-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="f71e0-343">La plantilla de dirección URL debe empezar con una barra diagonal (`/`).</span><span class="sxs-lookup"><span data-stu-id="f71e0-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="f71e0-344">Cuando se use un marcador de posición en la ruta de acceso, confirme que el punto de conexión (página o controlador) puede procesar el segmento de línea.</span><span class="sxs-lookup"><span data-stu-id="f71e0-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="f71e0-345">Por ejemplo, una página de Razor de errores debe aceptar el valor de segmento de línea opcional con la directiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="f71e0-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="f71e0-346">El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f71e0-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

<span data-ttu-id="f71e0-347">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="f71e0-347">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f71e0-348">Los verbos explícitos impiden que algunas solicitudes lleguen al método.</span><span class="sxs-lookup"><span data-stu-id="f71e0-348">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f71e0-349">Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.</span><span class="sxs-lookup"><span data-stu-id="f71e0-349">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="f71e0-350">Deshabilitar las páginas de códigos de estado</span><span class="sxs-lookup"><span data-stu-id="f71e0-350">Disable status code pages</span></span>

<span data-ttu-id="f71e0-351">Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="f71e0-351">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f71e0-352">Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="f71e0-352">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="f71e0-353">Código de control de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-353">Exception-handling code</span></span>

<span data-ttu-id="f71e0-354">El código de las páginas de control de excepciones puede producir excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-354">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="f71e0-355">Es recomendable que las páginas de errores de producción incluyan únicamente contenido estático.</span><span class="sxs-lookup"><span data-stu-id="f71e0-355">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="f71e0-356">Encabezados de respuesta</span><span class="sxs-lookup"><span data-stu-id="f71e0-356">Response headers</span></span>

<span data-ttu-id="f71e0-357">Una vez enviados los encabezados de una respuesta:</span><span class="sxs-lookup"><span data-stu-id="f71e0-357">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f71e0-358">La aplicación no puede cambiar el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="f71e0-358">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f71e0-359">No se pueden ejecutar páginas o controladores de excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-359">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f71e0-360">Deberá completarse la respuesta o anularse la conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-360">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f71e0-361">Control de excepciones del servidor</span><span class="sxs-lookup"><span data-stu-id="f71e0-361">Server exception handling</span></span>

<span data-ttu-id="f71e0-362">Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-362">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f71e0-363">Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta *500 - Error interno del servidor* sin cuerpo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-363">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="f71e0-364">Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="f71e0-364">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f71e0-365">El servidor controla las solicitudes que no controla la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-365">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="f71e0-366">El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f71e0-366">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f71e0-367">Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="f71e0-367">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f71e0-368">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="f71e0-368">Startup exception handling</span></span>

<span data-ttu-id="f71e0-369">Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f71e0-369">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f71e0-370">El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f71e0-370">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f71e0-371">La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host.</span><span class="sxs-lookup"><span data-stu-id="f71e0-371">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f71e0-372">Si se produce un error de enlace:</span><span class="sxs-lookup"><span data-stu-id="f71e0-372">If binding fails:</span></span>

* <span data-ttu-id="f71e0-373">La capa de hospedaje registra una excepción crítica.</span><span class="sxs-lookup"><span data-stu-id="f71e0-373">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f71e0-374">El proceso de dotnet se bloquea.</span><span class="sxs-lookup"><span data-stu-id="f71e0-374">The dotnet process crashes.</span></span>
* <span data-ttu-id="f71e0-375">No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f71e0-375">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f71e0-376">Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="f71e0-376">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f71e0-377">Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-377">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f71e0-378">Página de error de la base de datos</span><span class="sxs-lookup"><span data-stu-id="f71e0-378">Database error page</span></span>

<span data-ttu-id="f71e0-379">El middleware de la página de error de la base de datos captura excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f71e0-379">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="f71e0-380">Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="f71e0-380">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="f71e0-381">Esta página debe habilitarse solo en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="f71e0-381">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="f71e0-382">Habilitar la página mediante la adición de código a `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f71e0-382">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="f71e0-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiere el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="f71e0-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="f71e0-384">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="f71e0-384">Exception filters</span></span>

<span data-ttu-id="f71e0-385">En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción.</span><span class="sxs-lookup"><span data-stu-id="f71e0-385">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f71e0-386">En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="f71e0-386">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f71e0-387">Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro.</span><span class="sxs-lookup"><span data-stu-id="f71e0-387">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f71e0-388">Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f71e0-388">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="f71e0-389">Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="f71e0-389">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="f71e0-390">Se recomienda usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="f71e0-390">We recommend using the middleware.</span></span> <span data-ttu-id="f71e0-391">Use filtros únicamente cuando deba realizar el control de errores de manera diferente según la acción de MVC elegida.</span><span class="sxs-lookup"><span data-stu-id="f71e0-391">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="f71e0-392">Errores de estado del modelo</span><span class="sxs-lookup"><span data-stu-id="f71e0-392">Model state errors</span></span>

<span data-ttu-id="f71e0-393">Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f71e0-393">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f71e0-394">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f71e0-394">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
