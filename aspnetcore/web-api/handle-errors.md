---
title: Control de errores en API web de ASP.NET Core
author: pranavkm
description: Obtenga información sobre el control de errores con API web de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058381"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="7a9ed-103">Control de errores en API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a9ed-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="7a9ed-104">En este artículo se describe cómo administrar y personalizar el control de errores con API web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="7a9ed-105">[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7a9ed-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="7a9ed-106">Página de excepciones para el desarrollador</span><span class="sxs-lookup"><span data-stu-id="7a9ed-106">Developer Exception Page</span></span>

<span data-ttu-id="7a9ed-107">La [Página de excepciones para el desarrollador](xref:fundamentals/error-handling) es una herramienta útil para obtener seguimientos de pila detallados de los errores del servidor.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="7a9ed-108">Usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> para capturar excepciones sincrónicas y asincrónicas de la canalización HTTP y para generar respuestas de error.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="7a9ed-109">A modo de ejemplo, observe la siguiente acción del controlador:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="7a9ed-110">Ejecute el siguiente comando `curl` para probar la acción anterior:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9ed-111">En ASP.NET Core 3.0 y versiones posteriores, en la Página de excepciones para el desarrollador se muestra una respuesta de texto sin formato si el cliente no solicita la salida con formato HTML.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="7a9ed-112">Se mostrará la siguiente salida:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="7a9ed-113">Para mostrar una respuesta con formato HTML, establezca el encabezado de solicitud HTTP `Accept` en el tipo de medio `text/html`.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="7a9ed-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="7a9ed-115">El siguiente fragmento de la respuesta HTTP es importante:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7a9ed-116">En ASP.NET Core 2.2 y versiones anteriores, en la Página de excepciones para el desarrollador se muestra una respuesta con formato HTML.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="7a9ed-117">Por ejemplo, el siguiente fragmento de la respuesta HTTP es importante:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9ed-118">La respuesta con formato HTML resulta útil al realizar pruebas mediante herramientas como Postman.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="7a9ed-119">En la siguiente captura de pantalla se muestra tanto el texto sin formato como las respuestas con formato HTML en Postman:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Prueba de la Página de excepciones para el desarrollador en Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="7a9ed-121">Habilite la página de excepciones para el desarrollador **solo cuando la aplicación se ejecute en el entorno de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="7a9ed-122">No comparta información detallada de la excepción públicamente cuando la aplicación se ejecute en producción.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="7a9ed-123">Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="7a9ed-124">No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="7a9ed-125">Los verbos explícitos impiden que algunas solicitudes lleguen al método de acción.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="7a9ed-126">Permita el acceso anónimo al método si los usuarios no autenticados deberían ver el error.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="7a9ed-127">Controlador de excepciones</span><span class="sxs-lookup"><span data-stu-id="7a9ed-127">Exception handler</span></span>

<span data-ttu-id="7a9ed-128">En entornos que no son de desarrollo, se puede usar [middleware de control de excepciones](xref:fundamentals/error-handling) para producir una carga de error:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="7a9ed-129">En `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> para usar el middleware:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="7a9ed-130">Configure una acción de controlador para responder a la ruta `/error`:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="7a9ed-131">La acción `Error` anterior envía una carga compatible con [RFC 7807](https://tools.ietf.org/html/rfc7807) al cliente.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="7a9ed-132">El middleware de control de excepciones también puede proporcionar una salida negociada de contenido más detallada en el entorno de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="7a9ed-133">Use los pasos siguientes para generar un formato de carga coherente en los entornos de desarrollo y producción:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="7a9ed-134">En `Startup.Configure`, registre las instancias de middleware de control de excepciones específicas del entorno:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="7a9ed-135">En el código anterior, el middleware se ha registrado con:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="7a9ed-136">Una ruta de `/error-local-development` en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="7a9ed-137">Una ruta de `/error` en entornos que no son de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="7a9ed-138">Aplique el enrutamiento de atributos a acciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="7a9ed-139">El código anterior llama a [ControllerBase. problema](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) para crear una <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> respuesta.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="7a9ed-140">Uso de excepciones para modificar la respuesta</span><span class="sxs-lookup"><span data-stu-id="7a9ed-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="7a9ed-141">El contenido de la respuesta se puede modificar desde fuera del controlador.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="7a9ed-142">En la API web de ASP.NET 4.x, una manera de hacerlo era usar el tipo <xref:System.Web.Http.HttpResponseException>.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="7a9ed-143">ASP.NET Core no incluye un tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="7a9ed-144">Se pueden agregar compatibilidad para `HttpResponseException` con los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="7a9ed-145">Cree un tipo de excepción conocido denominado `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="7a9ed-146">Cree un filtro de acción denominado `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="7a9ed-147">El filtro anterior especifica una `Order` del valor entero máximo menos 10.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="7a9ed-148">Esto permite que otros filtros se ejecuten al final de la canalización.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="7a9ed-149">En `Startup.ConfigureServices`, agregue el filtro de acción a la colección de filtros:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="7a9ed-150">Respuesta de error ante errores de validación</span><span class="sxs-lookup"><span data-stu-id="7a9ed-150">Validation failure error response</span></span>

<span data-ttu-id="7a9ed-151">En el caso de los controladores de API web, MVC responde con un tipo de respuesta <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> cuando se produce un error en la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="7a9ed-152">MVC usa los resultados de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para construir la respuesta de error para un error de validación.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="7a9ed-153">En el ejemplo siguiente se usa la fábrica para cambiar el tipo de respuesta predeterminado a <xref:Microsoft.AspNetCore.Mvc.SerializableError> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="7a9ed-154">Respuesta de error del cliente</span><span class="sxs-lookup"><span data-stu-id="7a9ed-154">Client error response</span></span>

<span data-ttu-id="7a9ed-155">Se define un *resultado error* como un resultado con un código de estado HTTP de 400 o superior.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="7a9ed-156">En el caso de los controladores de API web, MVC transforma un resultado de un error en un resultado con <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="7a9ed-157">ASP.NET Core 2.1 genera una respuesta con los detalles del problema que es prácticamente compatible con RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="7a9ed-158">Si es importante que sea compatible al 100 %, actualice el proyecto a ASP.NET Core 2.2 o posterior.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9ed-159">La respuesta de error se puede configurar de una de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="7a9ed-160">Implementar ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="7a9ed-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="7a9ed-161">Usar ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="7a9ed-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="7a9ed-162">Implemente `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="7a9ed-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="7a9ed-163">MVC usa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> para generar todas las instancias de <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> y <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="7a9ed-164">Esto incluye las respuestas de error del cliente, las respuestas de error ante errores de validación y los métodos auxiliares <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="7a9ed-165">Para personalizar la respuesta de detalles del problema, registre una implementación personalizada de <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7a9ed-166">La respuesta de error se puede configurar como se describe en la sección [Uso de ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).</span><span class="sxs-lookup"><span data-stu-id="7a9ed-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="7a9ed-167">Uso de ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="7a9ed-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="7a9ed-168">Use la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> para configurar el contenido de la respuesta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="7a9ed-169">Por ejemplo, el código siguiente de `Startup.ConfigureServices` permite actualizar la propiedad `type` para respuestas 404:</span><span class="sxs-lookup"><span data-stu-id="7a9ed-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="7a9ed-170">Middleware personalizado para controlar las excepciones</span><span class="sxs-lookup"><span data-stu-id="7a9ed-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="7a9ed-171">Los valores predeterminados en el middleware de control de excepciones funcionan bien para la mayoría de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="7a9ed-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="7a9ed-172">En el caso de las aplicaciones que requieren un control de excepciones especializado, considere la posibilidad de [personalizar el middleware de control de excepciones](xref:fundamentals/error-handling#exception-handler-lambda).</span><span class="sxs-lookup"><span data-stu-id="7a9ed-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
