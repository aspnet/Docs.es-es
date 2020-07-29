---
title: Controlar errores en ASP.NET Core
author: rick-anderson
description: Descubra cómo controlar errores en aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: fundamentals/error-handling
ms.openlocfilehash: 857b4935816d0010fa425ce98f938c87eb35a581
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793453"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="3da90-103">Controlar errores en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3da90-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="3da90-104">Por [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3da90-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3da90-105">Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3da90-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="3da90-106">Consulte <xref:web-api/handle-errors> para las API web.</span><span class="sxs-lookup"><span data-stu-id="3da90-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="3da90-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="3da90-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="3da90-108">([Cómo descargarlo](xref:index#how-to-download-a-sample)). El artículo incluye instrucciones sobre cómo establecer directivas de preprocesador (`#if`, `#endif` y `#define`) en la aplicación de ejemplo para habilitar escenarios diferentes.</span><span class="sxs-lookup"><span data-stu-id="3da90-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="3da90-109">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="3da90-109">Developer Exception Page</span></span>

<span data-ttu-id="3da90-110">En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3da90-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="3da90-111">La página está disponible mediante el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que se encuentra en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3da90-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="3da90-112">Agregue código al método `Startup.Configure` para habilitar la página cuando la aplicación se ejecuta en el [entorno](xref:fundamentals/environments) de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="3da90-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="3da90-113">Realice una llamada a <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> antes de cualquier middleware donde quiera capturar excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="3da90-114">Habilite la página de excepciones para el desarrollador **solo cuando la aplicación se ejecute en el entorno de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="3da90-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="3da90-115">No le interesa compartir públicamente información detallada sobre las excepciones cuando la aplicación se ejecute en producción.</span><span class="sxs-lookup"><span data-stu-id="3da90-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="3da90-116">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3da90-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3da90-117">La página incluye la siguiente información sobre la excepción y la solicitud:</span><span class="sxs-lookup"><span data-stu-id="3da90-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="3da90-118">Seguimiento de la pila</span><span class="sxs-lookup"><span data-stu-id="3da90-118">Stack trace</span></span>
* <span data-ttu-id="3da90-119">Parámetros de cadena de consulta (si existen)</span><span class="sxs-lookup"><span data-stu-id="3da90-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="3da90-120">Cookies (si existen)</span><span class="sxs-lookup"><span data-stu-id="3da90-120">Cookies (if any)</span></span>
* <span data-ttu-id="3da90-121">Encabezados</span><span class="sxs-lookup"><span data-stu-id="3da90-121">Headers</span></span>

<span data-ttu-id="3da90-122">Para ver la Página de excepciones para el desarrollador en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use la directiva de preprocesador `DevEnvironment` y seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="3da90-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="3da90-123">Página del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="3da90-123">Exception handler page</span></span>

<span data-ttu-id="3da90-124">Para configurar una página de control de errores personalizada para el entorno de producción, use el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="3da90-125">El middleware:</span><span class="sxs-lookup"><span data-stu-id="3da90-125">The middleware:</span></span>

* <span data-ttu-id="3da90-126">Captura y registra las excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="3da90-127">Vuelve a ejecutar la solicitud en una canalización alternativa correspondiente a la página o el controlador indicados.</span><span class="sxs-lookup"><span data-stu-id="3da90-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="3da90-128">La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3da90-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="3da90-129">En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> agrega middleware de control de excepciones en entornos que no son de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="3da90-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="3da90-130">La plantilla de aplicación de [Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="3da90-130">The [Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="3da90-131">Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción para el error y una vista del error.</span><span class="sxs-lookup"><span data-stu-id="3da90-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="3da90-132">Este es el método de acción:</span><span class="sxs-lookup"><span data-stu-id="3da90-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="3da90-133">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="3da90-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="3da90-134">Los verbos explícitos impiden que algunas solicitudes lleguen al método.</span><span class="sxs-lookup"><span data-stu-id="3da90-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="3da90-135">Permita el acceso anónimo al método para que los usuarios no autenticados puedan recibir y ver el error.</span><span class="sxs-lookup"><span data-stu-id="3da90-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="3da90-136">Acceso a la excepción</span><span class="sxs-lookup"><span data-stu-id="3da90-136">Access the exception</span></span>

<span data-ttu-id="3da90-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso a la solicitud original y a la excepción en una página o un controlador de errores:</span><span class="sxs-lookup"><span data-stu-id="3da90-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="3da90-138">**No** proporcione información de errores confidencial a los clientes.</span><span class="sxs-lookup"><span data-stu-id="3da90-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="3da90-139">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="3da90-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="3da90-140">Para ver la página de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerPage` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="3da90-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="3da90-141">Lambda del controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="3da90-141">Exception handler lambda</span></span>

<span data-ttu-id="3da90-142">Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="3da90-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="3da90-143">Usar una expresión lambda permite acceder al error antes de devolver la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3da90-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="3da90-144">Este es un ejemplo del uso de una expresión lambda para el control de excepciones:</span><span class="sxs-lookup"><span data-stu-id="3da90-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="3da90-145">En el código anterior, se agrega `await context.Response.WriteAsync(new string(' ', 512));` para que el explorador Internet Explorer muestre el mensaje de error en lugar de un mensaje de error de IE.</span><span class="sxs-lookup"><span data-stu-id="3da90-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="3da90-146">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="3da90-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="3da90-147">**No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes.</span><span class="sxs-lookup"><span data-stu-id="3da90-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="3da90-148">Proporcionar información de los errores es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="3da90-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="3da90-149">Para ver el resultado de la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerLambda` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="3da90-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="3da90-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="3da90-150">UseStatusCodePages</span></span>

<span data-ttu-id="3da90-151">Una aplicación ASP.NET Core no proporciona de forma predeterminada una página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*.</span><span class="sxs-lookup"><span data-stu-id="3da90-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="3da90-152">La aplicación devuelve un código de estado y un cuerpo de respuesta vacío.</span><span class="sxs-lookup"><span data-stu-id="3da90-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="3da90-153">Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.</span><span class="sxs-lookup"><span data-stu-id="3da90-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="3da90-154">El middleware está disponible mediante el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que se encuentra en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3da90-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3da90-155">Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3da90-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="3da90-156">Llame a `UseStatusCodePages` antes del middleware de control de solicitudes (por ejemplo, middleware de archivos estáticos y middleware de MVC).</span><span class="sxs-lookup"><span data-stu-id="3da90-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="3da90-157">Este es un ejemplo del texto que muestran los controladores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="3da90-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="3da90-158">Para ver alguno de los distintos formatos de la página de códigos de estado en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use una de las directivas de preprocesador que comienzan por `StatusCodePages` y seleccione **Trigger a 404** (Desencadenar un error 404) en la página principal.</span><span class="sxs-lookup"><span data-stu-id="3da90-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="3da90-159">UseStatusCodePages con cadena de formato</span><span class="sxs-lookup"><span data-stu-id="3da90-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="3da90-160">Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que adopta una cadena de tipo de contenido y formato:</span><span class="sxs-lookup"><span data-stu-id="3da90-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="3da90-161">UseStatusCodePages con una expresión lambda</span><span class="sxs-lookup"><span data-stu-id="3da90-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="3da90-162">Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que adopta una expresión lambda:</span><span class="sxs-lookup"><span data-stu-id="3da90-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="3da90-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="3da90-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="3da90-164">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="3da90-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="3da90-165">Envía un código de estado *302 - Encontrado* al cliente.</span><span class="sxs-lookup"><span data-stu-id="3da90-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="3da90-166">Redirige al cliente a la ubicación proporcionada en la plantilla de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="3da90-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="3da90-167">La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, como se muestra en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="3da90-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="3da90-168">Si la plantilla de dirección URL comienza con una tilde (~), esta se sustituye por el atributo `PathBase` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3da90-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="3da90-169">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de [Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="3da90-169">If you point to an endpoint within the app, create an MVC view or [Razor page for the endpoint.</span></span> <span data-ttu-id="3da90-170">Para obtener un ejemplo de [Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="3da90-170">For a [Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="3da90-171">Este método se usa normalmente cuando la aplicación:</span><span class="sxs-lookup"><span data-stu-id="3da90-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="3da90-172">Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error.</span><span class="sxs-lookup"><span data-stu-id="3da90-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="3da90-173">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.</span><span class="sxs-lookup"><span data-stu-id="3da90-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="3da90-174">No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.</span><span class="sxs-lookup"><span data-stu-id="3da90-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="3da90-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="3da90-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="3da90-176">Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="3da90-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="3da90-177">Devuelve el código de estado original al cliente.</span><span class="sxs-lookup"><span data-stu-id="3da90-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="3da90-178">Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.</span><span class="sxs-lookup"><span data-stu-id="3da90-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="3da90-179">Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de [Razor para ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="3da90-179">If you point to an endpoint within the app, create an MVC view or [Razor page for the endpoint.</span></span> <span data-ttu-id="3da90-180">Para obtener un ejemplo de [Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="3da90-180">For a [Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="3da90-181">Este método se usa normalmente cuando la aplicación debe:</span><span class="sxs-lookup"><span data-stu-id="3da90-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="3da90-182">Procesar la solicitud sin redirigirla a un punto de conexión diferente.</span><span class="sxs-lookup"><span data-stu-id="3da90-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="3da90-183">En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="3da90-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="3da90-184">Conservar y devolver el código de estado original con la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3da90-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="3da90-185">Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición (`{0}`) relativo al código de estado.</span><span class="sxs-lookup"><span data-stu-id="3da90-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="3da90-186">La plantilla de dirección URL debe empezar con una barra diagonal (`/`).</span><span class="sxs-lookup"><span data-stu-id="3da90-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="3da90-187">Cuando se use un marcador de posición en la ruta de acceso, confirme que el punto de conexión (página o controlador) puede procesar el segmento de línea.</span><span class="sxs-lookup"><span data-stu-id="3da90-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="3da90-188">Por ejemplo, una página de [Razor de errores debe aceptar el valor de segmento de línea opcional con la directiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="3da90-188">For example, a [Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="3da90-189">El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3da90-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="3da90-190">Deshabilitar las páginas de códigos de estado</span><span class="sxs-lookup"><span data-stu-id="3da90-190">Disable status code pages</span></span>

<span data-ttu-id="3da90-191">Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3da90-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="3da90-192">Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de [Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="3da90-192">To disable status code pages for specific requests in a [Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="3da90-193">Código de control de excepciones</span><span class="sxs-lookup"><span data-stu-id="3da90-193">Exception-handling code</span></span>

<span data-ttu-id="3da90-194">El código de las páginas de control de excepciones puede producir excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="3da90-195">Es recomendable que las páginas de errores de producción incluyan únicamente contenido estático.</span><span class="sxs-lookup"><span data-stu-id="3da90-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="3da90-196">Encabezados de respuesta</span><span class="sxs-lookup"><span data-stu-id="3da90-196">Response headers</span></span>

<span data-ttu-id="3da90-197">Una vez enviados los encabezados de una respuesta:</span><span class="sxs-lookup"><span data-stu-id="3da90-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="3da90-198">La aplicación no puede cambiar el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3da90-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="3da90-199">No se pueden ejecutar páginas o controladores de excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="3da90-200">Deberá completarse la respuesta o anularse la conexión.</span><span class="sxs-lookup"><span data-stu-id="3da90-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="3da90-201">Control de excepciones del servidor</span><span class="sxs-lookup"><span data-stu-id="3da90-201">Server exception handling</span></span>

<span data-ttu-id="3da90-202">Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="3da90-203">Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta *500 - Error interno del servidor* sin cuerpo.</span><span class="sxs-lookup"><span data-stu-id="3da90-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="3da90-204">Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="3da90-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="3da90-205">El servidor controla las solicitudes que no controla la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3da90-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="3da90-206">El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3da90-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="3da90-207">Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="3da90-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="3da90-208">Control de excepciones de inicio</span><span class="sxs-lookup"><span data-stu-id="3da90-208">Startup exception handling</span></span>

<span data-ttu-id="3da90-209">Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3da90-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="3da90-210">El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="3da90-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="3da90-211">La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host.</span><span class="sxs-lookup"><span data-stu-id="3da90-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="3da90-212">Si se produce un error de enlace:</span><span class="sxs-lookup"><span data-stu-id="3da90-212">If binding fails:</span></span>

* <span data-ttu-id="3da90-213">La capa de hospedaje registra una excepción crítica.</span><span class="sxs-lookup"><span data-stu-id="3da90-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="3da90-214">El proceso de dotnet se bloquea.</span><span class="sxs-lookup"><span data-stu-id="3da90-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="3da90-215">No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="3da90-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="3da90-216">Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar.</span><span class="sxs-lookup"><span data-stu-id="3da90-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="3da90-217">Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="3da90-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="3da90-218">Página de error de la base de datos</span><span class="sxs-lookup"><span data-stu-id="3da90-218">Database error page</span></span>

<span data-ttu-id="3da90-219">El middleware de la página de error de la base de datos captura excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3da90-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="3da90-220">Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="3da90-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="3da90-221">Esta página debe habilitarse solo en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="3da90-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="3da90-222">Habilitar la página mediante la adición de código a `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3da90-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="3da90-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiere el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="3da90-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="3da90-224">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="3da90-224">Exception filters</span></span>

<span data-ttu-id="3da90-225">En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción.</span><span class="sxs-lookup"><span data-stu-id="3da90-225">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="3da90-226">En las aplicaciones de [Razor Pages, se pueden configurar a nivel global o por modelo de página.</span><span class="sxs-lookup"><span data-stu-id="3da90-226">In [Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="3da90-227">Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro.</span><span class="sxs-lookup"><span data-stu-id="3da90-227">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="3da90-228">Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="3da90-228">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="3da90-229">Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="3da90-229">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="3da90-230">Se recomienda usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="3da90-230">We recommend using the middleware.</span></span> <span data-ttu-id="3da90-231">Use filtros únicamente cuando deba realizar el control de errores de manera diferente según la acción de MVC elegida.</span><span class="sxs-lookup"><span data-stu-id="3da90-231">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="3da90-232">Errores de estado del modelo</span><span class="sxs-lookup"><span data-stu-id="3da90-232">Model state errors</span></span>

<span data-ttu-id="3da90-233">Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3da90-233">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3da90-234">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3da90-234">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
