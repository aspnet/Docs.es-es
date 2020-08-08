---
title: Control de errores en API web de ASP.NET Core
author: pranavkm
description: Obtenga información sobre el control de errores con API web de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 07/23/2020
no-loc:
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
ms.openlocfilehash: a17db9de5f19d11853fb3f9f8c45ade8391ff600
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021502"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Control de errores en API web de ASP.NET Core

En este artículo se describe cómo administrar y personalizar el control de errores con API web de ASP.NET Core.

[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Página de excepciones para el desarrollador

La [Página de excepciones para el desarrollador](xref:fundamentals/error-handling) es una herramienta útil para obtener seguimientos de pila detallados de los errores del servidor. Usa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> para capturar excepciones sincrónicas y asincrónicas de la canalización HTTP y para generar respuestas de error. A modo de ejemplo, observe la siguiente acción del controlador:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Ejecute el siguiente comando `curl` para probar la acción anterior:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

En ASP.NET Core 3.0 y versiones posteriores, en la Página de excepciones para el desarrollador se muestra una respuesta de texto sin formato si el cliente no solicita la salida con formato HTML. Aparece el siguiente resultado:

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

Para mostrar una respuesta con formato HTML, establezca el encabezado de solicitud HTTP `Accept` en el tipo de medio `text/html`. Por ejemplo:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

El siguiente fragmento de la respuesta HTTP es importante:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

En ASP.NET Core 2.2 y versiones anteriores, en la Página de excepciones para el desarrollador se muestra una respuesta con formato HTML. Por ejemplo, el siguiente fragmento de la respuesta HTTP es importante:

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

La respuesta con formato HTML resulta útil al realizar pruebas mediante herramientas como Postman. En la siguiente captura de pantalla se muestra tanto el texto sin formato como las respuestas con formato HTML en Postman:

![Prueba de la Página de excepciones para el desarrollador en Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Habilite la página de excepciones para el desarrollador **solo cuando la aplicación se ejecute en el entorno de desarrollo**. No le interesa compartir públicamente información detallada sobre las excepciones cuando la aplicación se ejecute en producción. Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.

## <a name="exception-handler"></a>Controlador de excepciones

En entornos que no son de desarrollo, se puede usar [middleware de control de excepciones](xref:fundamentals/error-handling) para producir una carga de error:

1. En `Startup.Configure`, invoque <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> para usar el middleware:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Configure una acción de controlador para responder a la ruta `/error`:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

La acción `Error` anterior envía una carga compatible con [RFC 7807](https://tools.ietf.org/html/rfc7807) al cliente.

El middleware de control de excepciones también puede proporcionar una salida negociada de contenido más detallada en el entorno de desarrollo local. Use los pasos siguientes para generar un formato de carga coherente en los entornos de desarrollo y producción:

1. En `Startup.Configure`, registre las instancias de middleware de control de excepciones específicas del entorno:

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

    En el código anterior, el middleware se ha registrado con:

    * Una ruta de `/error-local-development` en el entorno de desarrollo.
    * Una ruta de `/error` en entornos que no son de desarrollo.
    
1. Aplique el enrutamiento de atributos a acciones del controlador:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Uso de excepciones para modificar la respuesta

El contenido de la respuesta se puede modificar desde fuera del controlador. En la API web de ASP.NET 4.x, una manera de hacerlo era usar el tipo <xref:System.Web.Http.HttpResponseException>. ASP.NET Core no incluye un tipo equivalente. Se pueden agregar compatibilidad para `HttpResponseException` con los pasos siguientes:

1. Cree un tipo de excepción conocido denominado `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Cree un filtro de acción denominado `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    En el filtro anterior, el número mágico 10 se resta del valor entero máximo. Restar este número permite que otros filtros se ejecuten al final de la canalización.

1. En `Startup.ConfigureServices`, agregue el filtro de acción a la colección de filtros:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Respuesta de error ante errores de validación

En el caso de los controladores de API web, MVC responde con un tipo de respuesta <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> cuando se produce un error en la validación del modelo. MVC usa los resultados de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para construir la respuesta de error para un error de validación. En el ejemplo siguiente se usa la fábrica para cambiar el tipo de respuesta predeterminado a <xref:Microsoft.AspNetCore.Mvc.SerializableError> en `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Respuesta de error del cliente

Se define un *resultado error* como un resultado con un código de estado HTTP de 400 o superior. En el caso de los controladores de API web, MVC transforma un resultado de un error en un resultado con <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2.1 genera una respuesta con los detalles del problema que es prácticamente compatible con RFC 7807. Si es importante que sea compatible al 100 %, actualice el proyecto a ASP.NET Core 2.2 o posterior.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

La respuesta de error se puede configurar de una de las siguientes maneras:

1. [Implementar ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Usar ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implemente `ProblemDetailsFactory`

MVC usa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> para generar todas las instancias de <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> y <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. Esto incluye las respuestas de error del cliente, las respuestas de error ante errores de validación y los métodos auxiliares <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType>.

Para personalizar la respuesta de detalles del problema, registre una implementación personalizada de <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> en `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

La respuesta de error se puede configurar como se describe en la sección [Uso de ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Uso de ApiBehaviorOptions.ClientErrorMapping

Use la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> para configurar el contenido de la respuesta `ProblemDetails`. Por ejemplo, el código siguiente de `Startup.ConfigureServices` permite actualizar la propiedad `type` para respuestas 404:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
