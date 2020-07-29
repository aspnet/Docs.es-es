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
# <a name="handle-errors-in-aspnet-core"></a>Controlar errores en ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)

Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core. Consulte <xref:web-api/handle-errors> para las API web.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Cómo descargarlo](xref:index#how-to-download-a-sample)). El artículo incluye instrucciones sobre cómo establecer directivas de preprocesador (`#if`, `#endif` y `#define`) en la aplicación de ejemplo para habilitar escenarios diferentes.

## <a name="developer-exception-page"></a>Página de excepciones para el desarrollador

En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud. La página está disponible mediante el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que se encuentra en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Agregue código al método `Startup.Configure` para habilitar la página cuando la aplicación se ejecuta en el [entorno](xref:fundamentals/environments) de desarrollo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Realice una llamada a <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> antes de cualquier middleware donde quiera capturar excepciones.

> [!WARNING]
> Habilite la página de excepciones para el desarrollador **solo cuando la aplicación se ejecute en el entorno de desarrollo**. No le interesa compartir públicamente información detallada sobre las excepciones cuando la aplicación se ejecute en producción. Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.

La página incluye la siguiente información sobre la excepción y la solicitud:

* Seguimiento de la pila
* Parámetros de cadena de consulta (si existen)
* Cookies (si existen)
* Encabezados

Para ver la Página de excepciones para el desarrollador en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use la directiva de preprocesador `DevEnvironment` y seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="exception-handler-page"></a>Página del controlador de excepciones

Para configurar una página de control de errores personalizada para el entorno de producción, use el middleware de control de excepciones. El middleware:

* Captura y registra las excepciones.
* Vuelve a ejecutar la solicitud en una canalización alternativa correspondiente a la página o el controlador indicados. La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta.

En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> agrega middleware de control de excepciones en entornos que no son de desarrollo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

La plantilla de aplicación de [Razor Pages proporciona una página de error ( *.cshtml*) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*. Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción para el error y una vista del error. Este es el método de acción:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`. Los verbos explícitos impiden que algunas solicitudes lleguen al método. Permita el acceso anónimo al método para que los usuarios no autenticados puedan recibir y ver el error.

### <a name="access-the-exception"></a>Acceso a la excepción

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso a la solicitud original y a la excepción en una página o un controlador de errores:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **No** proporcione información de errores confidencial a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para ver la página de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerPage` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="exception-handler-lambda"></a>Lambda del controlador de excepciones

Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Usar una expresión lambda permite acceder al error antes de devolver la respuesta.

Este es un ejemplo del uso de una expresión lambda para el control de excepciones:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

En el código anterior, se agrega `await context.Response.WriteAsync(new string(' ', 512));` para que el explorador Internet Explorer muestre el mensaje de error en lugar de un mensaje de error de IE. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para ver el resultado de la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerLambda` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Una aplicación ASP.NET Core no proporciona de forma predeterminada una página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*. La aplicación devuelve un código de estado y un cuerpo de respuesta vacío. Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.

El middleware está disponible mediante el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que se encuentra en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> en el método `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Llame a `UseStatusCodePages` antes del middleware de control de solicitudes (por ejemplo, middleware de archivos estáticos y middleware de MVC).

Este es un ejemplo del texto que muestran los controladores predeterminados:

```
Status Code: 404; Not Found
```

Para ver alguno de los distintos formatos de la página de códigos de estado en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use una de las directivas de preprocesador que comienzan por `StatusCodePages` y seleccione **Trigger a 404** (Desencadenar un error 404) en la página principal.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages con cadena de formato

Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que adopta una cadena de tipo de contenido y formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages con una expresión lambda

Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que adopta una expresión lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Envía un código de estado *302 - Encontrado* al cliente.
* Redirige al cliente a la ubicación proporcionada en la plantilla de dirección URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, como se muestra en el ejemplo. Si la plantilla de dirección URL comienza con una tilde (~), esta se sustituye por el atributo `PathBase` de la aplicación. Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de [Razor para ese punto de conexión. Para obtener un ejemplo de [Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método se usa normalmente cuando la aplicación:

* Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.
* No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Devuelve el código de estado original al cliente.
* Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de [Razor para ese punto de conexión. Para obtener un ejemplo de [Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método se usa normalmente cuando la aplicación debe:

* Procesar la solicitud sin redirigirla a un punto de conexión diferente. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.
* Conservar y devolver el código de estado original con la respuesta.

Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición (`{0}`) relativo al código de estado. La plantilla de dirección URL debe empezar con una barra diagonal (`/`). Cuando se use un marcador de posición en la ruta de acceso, confirme que el punto de conexión (página o controlador) puede procesar el segmento de línea. Por ejemplo, una página de [Razor de errores debe aceptar el valor de segmento de línea opcional con la directiva `@page`:

```cshtml
@page "{code?}"
```

El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Deshabilitar las páginas de códigos de estado

Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de [Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Código de control de excepciones

El código de las páginas de control de excepciones puede producir excepciones. Es recomendable que las páginas de errores de producción incluyan únicamente contenido estático.

### <a name="response-headers"></a>Encabezados de respuesta

Una vez enviados los encabezados de una respuesta:

* La aplicación no puede cambiar el código de estado de la respuesta.
* No se pueden ejecutar páginas o controladores de excepciones. Deberá completarse la respuesta o anularse la conexión.

## <a name="server-exception-handling"></a>Control de excepciones del servidor

Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones. Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta *500 - Error interno del servidor* sin cuerpo. Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión. El servidor controla las solicitudes que no controla la aplicación. El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud. Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.

## <a name="startup-exception-handling"></a>Control de excepciones de inicio

Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación. El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).

La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host. Si se produce un error de enlace:

* La capa de hospedaje registra una excepción crítica.
* El proceso de dotnet se bloquea.
* No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).

Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar. Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Página de error de la base de datos

El middleware de la página de error de la base de datos captura excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework. Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema. Esta página debe habilitarse solo en el entorno de desarrollo. Habilitar la página mediante la adición de código a `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiere el paquete NuGet [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/).

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtros de excepciones

En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción. En las aplicaciones de [Razor Pages, se pueden configurar a nivel global o por modelo de página. Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro. Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el middleware de control de excepciones. Se recomienda usar el middleware. Use filtros únicamente cuando deba realizar el control de errores de manera diferente según la acción de MVC elegida.

## <a name="model-state-errors"></a>Errores de estado del modelo

Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
