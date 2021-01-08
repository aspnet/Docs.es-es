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
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753119"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="a4db2-103">Controlar errores en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4db2-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a4db2-104">De [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a4db2-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a4db2-105">Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4db2-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="a4db2-106">Consulte <xref:web-api/handle-errors> para las API web.</span><span class="sxs-lookup"><span data-stu-id="a4db2-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="a4db2-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a4db2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="a4db2-108">([Cómo descargarlo](xref:index#how-to-download-a-sample)). La pestaña Red de las herramientas de desarrollo del explorador F12 resulta útil al probar la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a4db2-109">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="a4db2-109">Developer Exception Page</span></span>

<span data-ttu-id="a4db2-110">En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a4db2-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="a4db2-111">Las plantillas de ASP.NET Core generan el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="a4db2-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="a4db2-112">El código resaltado anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a4db2-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a4db2-113">Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> al principio de la canalización de middleware para que pueda detectar las excepciones que se producen en el middleware que sigue.</span><span class="sxs-lookup"><span data-stu-id="a4db2-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="a4db2-114">El código anterior \***solo** habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="a4db2-115">La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en el entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="a4db2-116">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a4db2-117">La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:</span><span class="sxs-lookup"><span data-stu-id="a4db2-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="a4db2-118">_ Seguimiento de la pila</span><span class="sxs-lookup"><span data-stu-id="a4db2-118">_ Stack trace</span></span>
* <span data-ttu-id="a4db2-119">Parámetros de cadena de consulta (si existen)</span><span class="sxs-lookup"><span data-stu-id="a4db2-119">Query string parameters if any</span></span>
* <span data-ttu-id="a4db2-120">Cookie (si existen)</span><span class="sxs-lookup"><span data-stu-id="a4db2-120">Cookies if any</span></span>
* <span data-ttu-id="a4db2-121">Encabezados</span><span class="sxs-lookup"><span data-stu-id="a4db2-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="a4db2-122">Página del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-122">Exception handler page</span></span>

<span data-ttu-id="a4db2-123">Para configurar una página de control de errores personalizada para el [entorno de producción](xref:fundamentals/environments), llame a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="a4db2-124">Este middleware de control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="a4db2-124">This exception handling middleware:</span></span>

* <span data-ttu-id="a4db2-125">Captura y registra las excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="a4db2-126">Vuelve a ejecutar la solicitud en una canalización alternativa con la ruta de acceso indicada.</span><span class="sxs-lookup"><span data-stu-id="a4db2-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="a4db2-127">La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="a4db2-128">El código generado por la plantilla vuelve a ejecutar la solicitud mediante la ruta de acceso `/Error`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="a4db2-129">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="a4db2-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="a4db2-130">La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="a4db2-131">Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción de `Error` y una vista del error para el controlador de inicio.</span><span class="sxs-lookup"><span data-stu-id="a4db2-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="a4db2-132">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a4db2-133">Los verbos explícitos impiden que algunas solicitudes lleguen al método de acción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="a4db2-134">Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="a4db2-135">Acceso a la excepción</span><span class="sxs-lookup"><span data-stu-id="a4db2-135">Access the exception</span></span>

<span data-ttu-id="a4db2-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso de la solicitud original y a la excepción en un controlador de errores.</span><span class="sxs-lookup"><span data-stu-id="a4db2-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="a4db2-137">El código siguiente agrega `ExceptionMessage` al archivo *Pages/Error.cshtml.cs* predeterminado que se genera con las plantillas de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a4db2-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="a4db2-138">**No** proporcione información de errores confidencial a los clientes.</span><span class="sxs-lookup"><span data-stu-id="a4db2-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="a4db2-139">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="a4db2-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="a4db2-140">Para probar la excepción en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="a4db2-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="a4db2-141">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-141">Set the environment to production.</span></span>
* <span data-ttu-id="a4db2-142">Quite los comentarios de `webBuilder.UseStartup<Startup>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="a4db2-143">Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="a4db2-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="a4db2-144">Lambda del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-144">Exception handler lambda</span></span>

<span data-ttu-id="a4db2-145">Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="a4db2-146">Usar una expresión lambda permite acceder al error antes de devolver la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="a4db2-147">En el código siguiente se usa una expresión lambda para el control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="a4db2-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="a4db2-148">**No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes.</span><span class="sxs-lookup"><span data-stu-id="a4db2-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a4db2-149">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="a4db2-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="a4db2-150">Para probar la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="a4db2-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="a4db2-151">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-151">Set the environment to production.</span></span>
* <span data-ttu-id="a4db2-152">Quite los comentarios de `webBuilder.UseStartup<StartupLambda>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="a4db2-153">Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="a4db2-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="a4db2-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="a4db2-154">UseStatusCodePages</span></span>

<span data-ttu-id="a4db2-155">Una aplicación ASP.NET Core no proporciona de forma predeterminada ninguna página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="a4db2-156">Cuando la aplicación encuentra un código de estado de error HTTP 400-599 que no tiene cuerpo, devuelve el código de estado y un cuerpo de respuesta vacío.</span><span class="sxs-lookup"><span data-stu-id="a4db2-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="a4db2-157">Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="a4db2-158">Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a4db2-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="a4db2-159">Llame a `UseStatusCodePages` antes del middleware de control de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="a4db2-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="a4db2-160">Por ejemplo, llame a `UseStatusCodePages` antes del middleware de archivos estáticos y el middleware de puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="a4db2-161">Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="a4db2-162">Por ejemplo, al navegar a `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="a4db2-163">Cuando se llama a `UseStatusCodePages`, el explorador devuelve:</span><span class="sxs-lookup"><span data-stu-id="a4db2-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="a4db2-164">`UseStatusCodePages` no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="a4db2-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="a4db2-165">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="a4db2-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="a4db2-166">Establezca el entorno en producción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-166">Set the environment to production.</span></span>
* <span data-ttu-id="a4db2-167">Quite los comentarios de `webBuilder.UseStartup<StartupUseStatusCodePages>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="a4db2-168">Seleccione los vínculos en la página principal.</span><span class="sxs-lookup"><span data-stu-id="a4db2-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="a4db2-169">El middleware de páginas de códigos de estado **no** detecta excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="a4db2-170">Para proporcionar una página de control de errores personalizada, use la [página del controlador de excepciones](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="a4db2-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="a4db2-171">UseStatusCodePages con cadena de formato</span><span class="sxs-lookup"><span data-stu-id="a4db2-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="a4db2-172">Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:</span><span class="sxs-lookup"><span data-stu-id="a4db2-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="a4db2-173">En el código anterior, `{0}` es un marcador de posición para el código de error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="a4db2-174">`UseStatusCodePages` con una cadena de formato no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="a4db2-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="a4db2-175">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupFormat>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="a4db2-176">UseStatusCodePages con una expresión lambda</span><span class="sxs-lookup"><span data-stu-id="a4db2-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="a4db2-177">Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:</span><span class="sxs-lookup"><span data-stu-id="a4db2-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="a4db2-178">`UseStatusCodePages` con una expresión lambda no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="a4db2-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="a4db2-179">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupStatusLambda>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="a4db2-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="a4db2-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="a4db2-181">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="a4db2-182">Envía un código de estado [302 - Encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/302) al cliente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="a4db2-183">Redirige el cliente al punto de conexión de control de errores proporcionado en la plantilla de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="a4db2-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="a4db2-184">El punto de conexión de control de errores suele mostrar información de error y devuelve HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a4db2-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="a4db2-185">La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, tal y como se muestra en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="a4db2-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="a4db2-186">Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="a4db2-187">Si especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a4db2-188">Para obtener un ejemplo de Razor Pages, consulte [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="a4db2-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="a4db2-189">Este método se usa normalmente cuando la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a4db2-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="a4db2-190">Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="a4db2-191">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.</span><span class="sxs-lookup"><span data-stu-id="a4db2-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="a4db2-192">No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.</span><span class="sxs-lookup"><span data-stu-id="a4db2-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="a4db2-193">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCredirect>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="a4db2-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="a4db2-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="a4db2-195">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="a4db2-196">Devuelve el código de estado original al cliente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="a4db2-197">Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.</span><span class="sxs-lookup"><span data-stu-id="a4db2-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="a4db2-198">Si se especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a4db2-199">Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="a4db2-200">Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="a4db2-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="a4db2-201">Este método se usa normalmente cuando la aplicación debe:</span><span class="sxs-lookup"><span data-stu-id="a4db2-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="a4db2-202">Procesar la solicitud sin redirigirla a un punto de conexión diferente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="a4db2-203">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="a4db2-204">Conservar y devolver el código de estado original con la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="a4db2-205">Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición `{0}` relativo al código de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="a4db2-206">La plantilla de dirección URL debe empezar con `/`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="a4db2-207">El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a4db2-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="a4db2-208">Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="a4db2-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="a4db2-209">Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCreX>();` en *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="a4db2-210">Deshabilitar las páginas de códigos de estado</span><span class="sxs-lookup"><span data-stu-id="a4db2-210">Disable status code pages</span></span>

<span data-ttu-id="a4db2-211">Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="a4db2-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="a4db2-212">Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="a4db2-213">Código de control de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-213">Exception-handling code</span></span>

<span data-ttu-id="a4db2-214">El código de las páginas de control de excepciones también puede iniciar excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="a4db2-215">Las páginas de errores de producción se deben probar minuciosamente y se debe tener especial cuidado para evitar que inicien sus propias excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="a4db2-216">Encabezados de respuesta</span><span class="sxs-lookup"><span data-stu-id="a4db2-216">Response headers</span></span>

<span data-ttu-id="a4db2-217">Una vez enviados los encabezados de una respuesta:</span><span class="sxs-lookup"><span data-stu-id="a4db2-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="a4db2-218">La aplicación no puede cambiar el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="a4db2-219">No se pueden ejecutar páginas o controladores de excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="a4db2-220">Deberá completarse la respuesta o anularse la conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="a4db2-221">Control de excepciones del servidor</span><span class="sxs-lookup"><span data-stu-id="a4db2-221">Server exception handling</span></span>

<span data-ttu-id="a4db2-222">Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="a4db2-223">Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta `500 - Internal Server Error` sin cuerpo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="a4db2-224">Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="a4db2-225">El servidor controla las solicitudes que no controla la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="a4db2-226">El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a4db2-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="a4db2-227">Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="a4db2-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="a4db2-228">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="a4db2-228">Startup exception handling</span></span>

<span data-ttu-id="a4db2-229">Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="a4db2-230">El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="a4db2-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="a4db2-231">La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host.</span><span class="sxs-lookup"><span data-stu-id="a4db2-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="a4db2-232">Si se produce un error de enlace:</span><span class="sxs-lookup"><span data-stu-id="a4db2-232">If binding fails:</span></span>

* <span data-ttu-id="a4db2-233">La capa de hospedaje registra una excepción crítica.</span><span class="sxs-lookup"><span data-stu-id="a4db2-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="a4db2-234">El proceso de dotnet se bloquea.</span><span class="sxs-lookup"><span data-stu-id="a4db2-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="a4db2-235">No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a4db2-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a4db2-236">Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="a4db2-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="a4db2-237">Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="a4db2-238">Página de error de la base de datos</span><span class="sxs-lookup"><span data-stu-id="a4db2-238">Database error page</span></span>

<span data-ttu-id="a4db2-239">El filtro de excepciones `AddDatabaseDeveloperPageExceptionFilter` de la página del desarrollador de bases de datos captura las excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a4db2-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="a4db2-240">Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="a4db2-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="a4db2-241">Esta página debe habilitarse solo en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="a4db2-242">El siguiente código fue generado por las plantillas de Razor Pages de ASP.NET Core cuando se especificaron cuentas de usuario individuales:</span><span class="sxs-lookup"><span data-stu-id="a4db2-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="a4db2-243">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-243">Exception filters</span></span>

<span data-ttu-id="a4db2-244">En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="a4db2-245">En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="a4db2-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="a4db2-246">Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro.</span><span class="sxs-lookup"><span data-stu-id="a4db2-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="a4db2-247">Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="a4db2-248">Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el [middleware de control de excepciones](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) integrado, `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="a4db2-249">Se recomienda usar `UseExceptionHandler`, a no ser que tenga que realizar el control de errores de otra forma según la acción de MVC elegida.</span><span class="sxs-lookup"><span data-stu-id="a4db2-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="a4db2-250">Errores de estado del modelo</span><span class="sxs-lookup"><span data-stu-id="a4db2-250">Model state errors</span></span>

<span data-ttu-id="a4db2-251">Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="a4db2-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4db2-252">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a4db2-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a4db2-253">De [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a4db2-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a4db2-254">Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4db2-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="a4db2-255">Consulte <xref:web-api/handle-errors> para las API web.</span><span class="sxs-lookup"><span data-stu-id="a4db2-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="a4db2-256">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a4db2-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="a4db2-257">([Cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a4db2-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a4db2-258">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="a4db2-258">Developer Exception Page</span></span>

<span data-ttu-id="a4db2-259">En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a4db2-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="a4db2-260">Las plantillas de ASP.NET Core generan el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="a4db2-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="a4db2-261">El código anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a4db2-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a4db2-262">Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> antes que cualquier middleware para que las excepciones se detecten en el middleware que sigue.</span><span class="sxs-lookup"><span data-stu-id="a4db2-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="a4db2-263">El código anterior solo habilita la página de excepciones para el desarrollador **cuando la aplicación se ejecuta en el entorno de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="a4db2-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a4db2-264">La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en producción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="a4db2-265">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a4db2-266">La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:</span><span class="sxs-lookup"><span data-stu-id="a4db2-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="a4db2-267">Seguimiento de la pila</span><span class="sxs-lookup"><span data-stu-id="a4db2-267">Stack trace</span></span>
* <span data-ttu-id="a4db2-268">Parámetros de cadena de consulta (si existen)</span><span class="sxs-lookup"><span data-stu-id="a4db2-268">Query string parameters if any</span></span>
* <span data-ttu-id="a4db2-269">Cookie (si existen)</span><span class="sxs-lookup"><span data-stu-id="a4db2-269">Cookies if any</span></span>
* <span data-ttu-id="a4db2-270">Encabezados</span><span class="sxs-lookup"><span data-stu-id="a4db2-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="a4db2-271">Página del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-271">Exception handler page</span></span>

<span data-ttu-id="a4db2-272">Para configurar una página de control de errores personalizada para el entorno de producción, use el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="a4db2-273">El middleware:</span><span class="sxs-lookup"><span data-stu-id="a4db2-273">The middleware:</span></span>

* <span data-ttu-id="a4db2-274">Captura y registra las excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="a4db2-275">Vuelve a ejecutar la solicitud en una canalización alternativa correspondiente a la página o el controlador indicados.</span><span class="sxs-lookup"><span data-stu-id="a4db2-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="a4db2-276">La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="a4db2-277">El código generado por la plantilla vuelve a ejecutar la solicitud para `/Error`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="a4db2-278">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="a4db2-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="a4db2-279">La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="a4db2-280">Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción para el error y una vista del error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="a4db2-281">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a4db2-282">Los verbos explícitos impiden que algunas solicitudes lleguen al método.</span><span class="sxs-lookup"><span data-stu-id="a4db2-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="a4db2-283">Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="a4db2-284">Acceso a la excepción</span><span class="sxs-lookup"><span data-stu-id="a4db2-284">Access the exception</span></span>

<span data-ttu-id="a4db2-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso a la solicitud original y a la excepción en una página o un controlador de errores:</span><span class="sxs-lookup"><span data-stu-id="a4db2-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="a4db2-286">**No** proporcione información de errores confidencial a los clientes.</span><span class="sxs-lookup"><span data-stu-id="a4db2-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="a4db2-287">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="a4db2-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="a4db2-288">Para desencadenar la página de control de excepciones anterior, establezca el entorno en producciones y fuerce una excepción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="a4db2-289">Lambda del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-289">Exception handler lambda</span></span>

<span data-ttu-id="a4db2-290">Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="a4db2-291">Usar una expresión lambda permite acceder al error antes de devolver la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="a4db2-292">Este es un ejemplo del uso de una expresión lambda para el control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="a4db2-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="a4db2-293">En el código anterior, se agrega `await context.Response.WriteAsync(new string(' ', 512));` para que el explorador Internet Explorer muestre el mensaje de error en lugar de un mensaje de error de IE.</span><span class="sxs-lookup"><span data-stu-id="a4db2-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="a4db2-294">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="a4db2-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="a4db2-295">**No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes.</span><span class="sxs-lookup"><span data-stu-id="a4db2-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a4db2-296">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="a4db2-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="a4db2-297">Para ver el resultado de la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerLambda` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="a4db2-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="a4db2-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="a4db2-298">UseStatusCodePages</span></span>

<span data-ttu-id="a4db2-299">Una aplicación ASP.NET Core no proporciona de forma predeterminada una página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="a4db2-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="a4db2-300">La aplicación devuelve un código de estado y un cuerpo de respuesta vacío.</span><span class="sxs-lookup"><span data-stu-id="a4db2-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="a4db2-301">Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="a4db2-302">El middleware está disponible en el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/).</span><span class="sxs-lookup"><span data-stu-id="a4db2-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="a4db2-303">Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a4db2-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="a4db2-304">Llame a `UseStatusCodePages` antes del middleware de control de solicitudes (por ejemplo, middleware de archivos estáticos y middleware de MVC).</span><span class="sxs-lookup"><span data-stu-id="a4db2-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="a4db2-305">Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="a4db2-306">Por ejemplo, al navegar a `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="a4db2-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="a4db2-307">Cuando se llama a `UseStatusCodePages`, el explorador devuelve:</span><span class="sxs-lookup"><span data-stu-id="a4db2-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="a4db2-308">UseStatusCodePages con cadena de formato</span><span class="sxs-lookup"><span data-stu-id="a4db2-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="a4db2-309">Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:</span><span class="sxs-lookup"><span data-stu-id="a4db2-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="a4db2-310">UseStatusCodePages con una expresión lambda</span><span class="sxs-lookup"><span data-stu-id="a4db2-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="a4db2-311">Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:</span><span class="sxs-lookup"><span data-stu-id="a4db2-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="a4db2-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="a4db2-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="a4db2-313">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="a4db2-314">Envía un código de estado *302 - Encontrado* al cliente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="a4db2-315">Redirige al cliente a la ubicación proporcionada en la plantilla de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="a4db2-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="a4db2-316">La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, como se muestra en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="a4db2-317">Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="a4db2-318">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a4db2-319">Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a4db2-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a4db2-320">Este método se usa normalmente cuando la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a4db2-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="a4db2-321">Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error.</span><span class="sxs-lookup"><span data-stu-id="a4db2-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="a4db2-322">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.</span><span class="sxs-lookup"><span data-stu-id="a4db2-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="a4db2-323">No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.</span><span class="sxs-lookup"><span data-stu-id="a4db2-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="a4db2-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="a4db2-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="a4db2-325">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="a4db2-326">Devuelve el código de estado original al cliente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="a4db2-327">Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.</span><span class="sxs-lookup"><span data-stu-id="a4db2-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="a4db2-328">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a4db2-329">Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="a4db2-330">Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a4db2-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a4db2-331">Este método se usa normalmente cuando la aplicación debe:</span><span class="sxs-lookup"><span data-stu-id="a4db2-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="a4db2-332">Procesar la solicitud sin redirigirla a un punto de conexión diferente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="a4db2-333">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="a4db2-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="a4db2-334">Conservar y devolver el código de estado original con la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="a4db2-335">Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición (`{0}`) relativo al código de estado.</span><span class="sxs-lookup"><span data-stu-id="a4db2-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="a4db2-336">La plantilla de dirección URL debe empezar con una barra diagonal (`/`).</span><span class="sxs-lookup"><span data-stu-id="a4db2-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="a4db2-337">Cuando se use un marcador de posición en la ruta de acceso, confirme que el punto de conexión (página o controlador) puede procesar el segmento de línea.</span><span class="sxs-lookup"><span data-stu-id="a4db2-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="a4db2-338">Por ejemplo, una página de Razor de errores debe aceptar el valor de segmento de línea opcional con la directiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="a4db2-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="a4db2-339">El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a4db2-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="a4db2-340">Deshabilitar las páginas de códigos de estado</span><span class="sxs-lookup"><span data-stu-id="a4db2-340">Disable status code pages</span></span>

<span data-ttu-id="a4db2-341">Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="a4db2-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="a4db2-342">Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="a4db2-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="a4db2-343">Código de control de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-343">Exception-handling code</span></span>

<span data-ttu-id="a4db2-344">El código de las páginas de control de excepciones puede producir excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="a4db2-345">Es recomendable que las páginas de errores de producción incluyan únicamente contenido estático.</span><span class="sxs-lookup"><span data-stu-id="a4db2-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="a4db2-346">Encabezados de respuesta</span><span class="sxs-lookup"><span data-stu-id="a4db2-346">Response headers</span></span>

<span data-ttu-id="a4db2-347">Una vez enviados los encabezados de una respuesta:</span><span class="sxs-lookup"><span data-stu-id="a4db2-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="a4db2-348">La aplicación no puede cambiar el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="a4db2-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="a4db2-349">No se pueden ejecutar páginas o controladores de excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="a4db2-350">Deberá completarse la respuesta o anularse la conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="a4db2-351">Control de excepciones del servidor</span><span class="sxs-lookup"><span data-stu-id="a4db2-351">Server exception handling</span></span>

<span data-ttu-id="a4db2-352">Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="a4db2-353">Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta *500 - Error interno del servidor* sin cuerpo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="a4db2-354">Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="a4db2-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="a4db2-355">El servidor controla las solicitudes que no controla la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="a4db2-356">El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud.</span><span class="sxs-lookup"><span data-stu-id="a4db2-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="a4db2-357">Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="a4db2-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="a4db2-358">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="a4db2-358">Startup exception handling</span></span>

<span data-ttu-id="a4db2-359">Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4db2-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="a4db2-360">El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="a4db2-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="a4db2-361">La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host.</span><span class="sxs-lookup"><span data-stu-id="a4db2-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="a4db2-362">Si se produce un error de enlace:</span><span class="sxs-lookup"><span data-stu-id="a4db2-362">If binding fails:</span></span>

* <span data-ttu-id="a4db2-363">La capa de hospedaje registra una excepción crítica.</span><span class="sxs-lookup"><span data-stu-id="a4db2-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="a4db2-364">El proceso de dotnet se bloquea.</span><span class="sxs-lookup"><span data-stu-id="a4db2-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="a4db2-365">No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a4db2-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a4db2-366">Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="a4db2-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="a4db2-367">Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="a4db2-368">Página de error de la base de datos</span><span class="sxs-lookup"><span data-stu-id="a4db2-368">Database error page</span></span>

<span data-ttu-id="a4db2-369">El middleware de la página de error de la base de datos captura excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a4db2-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="a4db2-370">Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="a4db2-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="a4db2-371">Esta página debe habilitarse solo en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a4db2-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="a4db2-372">Habilitar la página mediante la adición de código a `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a4db2-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="a4db2-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiere el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a4db2-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="a4db2-374">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="a4db2-374">Exception filters</span></span>

<span data-ttu-id="a4db2-375">En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción.</span><span class="sxs-lookup"><span data-stu-id="a4db2-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="a4db2-376">En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="a4db2-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="a4db2-377">Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro.</span><span class="sxs-lookup"><span data-stu-id="a4db2-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="a4db2-378">Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="a4db2-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="a4db2-379">Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="a4db2-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="a4db2-380">Se recomienda usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="a4db2-380">We recommend using the middleware.</span></span> <span data-ttu-id="a4db2-381">Use filtros únicamente cuando deba realizar el control de errores de manera diferente según la acción de MVC elegida.</span><span class="sxs-lookup"><span data-stu-id="a4db2-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="a4db2-382">Errores de estado del modelo</span><span class="sxs-lookup"><span data-stu-id="a4db2-382">Model state errors</span></span>

<span data-ttu-id="a4db2-383">Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="a4db2-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4db2-384">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a4db2-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
