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
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060474"
---
# <a name="handle-errors-in-aspnet-core"></a>Controlar errores en ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

De [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)

Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core. Consulte <xref:web-api/handle-errors> para las API web.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Cómo descargarlo](xref:index#how-to-download-a-sample)). La pestaña Red de las herramientas de desarrollo del explorador F12 resulta útil al probar la aplicación de ejemplo.

## <a name="developer-exception-page"></a>Página de excepciones para el desarrollador

En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud. Las plantillas de ASP.NET Core generan el siguiente código:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

El código resaltado anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).

Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> al principio de la canalización de middleware para que pueda detectar las excepciones que se producen en el middleware que sigue.

El código anterior * **solo** habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el entorno de desarrollo. La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en el entorno de producción. Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.

La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:

_ Seguimiento de la pila
* Parámetros de cadena de consulta (si existen)
* Cookie (si existen)
* Encabezados

## <a name="exception-handler-page"></a>Página del controlador de excepciones

Para configurar una página de control de errores personalizada para el [entorno de producción](xref:fundamentals/environments), llame a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Este middleware de control de excepciones:

* Captura y registra las excepciones.
* Vuelve a ejecutar la solicitud en una canalización alternativa con la ruta de acceso indicada. La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta. El código generado por la plantilla vuelve a ejecutar la solicitud mediante la ruta de acceso `/Error`.

En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml* ) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*. Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción de `Error` y una vista del error para el controlador de inicio.

No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`. Los verbos explícitos impiden que algunas solicitudes lleguen al método de acción. Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.

### <a name="access-the-exception"></a>Acceso a la excepción

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso de la solicitud original y a la excepción en un controlador de errores. El código siguiente agrega `ExceptionMessage` al archivo *Pages/Error.cshtml.cs* predeterminado que se genera con las plantillas de ASP.NET Core:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> **No** proporcione información de errores confidencial a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para probar la excepción en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Establezca el entorno en producción.
* Quite los comentarios de `webBuilder.UseStartup<Startup>();` en *Program.cs*.
* Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="exception-handler-lambda"></a>Lambda del controlador de excepciones

Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Usar una expresión lambda permite acceder al error antes de devolver la respuesta.

En el código siguiente se usa una expresión lambda para el control de excepciones:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> **No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para probar la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Establezca el entorno en producción.
* Quite los comentarios de `webBuilder.UseStartup<StartupLambda>();` en *Program.cs*.
* Seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Una aplicación ASP.NET Core no proporciona de forma predeterminada ninguna página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*. Cuando la aplicación encuentra una condición de error HTTP 400-499 que no tiene cuerpo, devuelve el código de estado y un cuerpo de respuesta vacío. Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado. Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

Llame a `UseStatusCodePages` antes del middleware de control de solicitudes. Por ejemplo, llame a `UseStatusCodePages` antes del middleware de archivos estáticos y el middleware de puntos de conexión.

Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión. Por ejemplo, al navegar a `Home/Privacy2`. Cuando se llama a `UseStatusCodePages`, el explorador devuelve:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.

Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Establezca el entorno en producción.
* Quite los comentarios de `webBuilder.UseStartup<StartupUseStatusCodePages>();` en *Program.cs*.
* Seleccione los vínculos en la página principal.

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages con cadena de formato

Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

En el código anterior, `{0}` es un marcador de posición para el código de error.

`UseStatusCodePages` con una cadena de formato no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.

Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupFormat>();` en *Program.cs*.

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages con una expresión lambda

Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` con una expresión lambda no se utiliza normalmente en producción, ya que devuelve un mensaje que no es útil para los usuarios.

Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupStatusLambda>();` en *Program.cs*.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Envía un código de estado [302 - Encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/302) al cliente.
* Redirige el cliente al punto de conexión de control de errores proporcionado en la plantilla de dirección URL. El punto de conexión de control de errores suele mostrar información de error y devuelve HTTP 200.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, tal y como se muestra en el código anterior. Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación. Si especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión. Para obtener un ejemplo de Razor Pages, consulte [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Este método se usa normalmente cuando la aplicación:

* Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.
* No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.

Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCredirect>();` en *Program.cs*.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Devuelve el código de estado original al cliente.
* Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Si se especifica un punto de conexión en la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión. Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado. Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Este método se usa normalmente cuando la aplicación debe:

* Procesar la solicitud sin redirigirla a un punto de conexión diferente. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.
* Conservar y devolver el código de estado original con la respuesta.

Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición `{0}` relativo al código de estado. La plantilla de dirección URL debe empezar con `/`.

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Para ver un ejemplo de Razor Pages, consulte [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Para probar `UseStatusCodePages` en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), quite los comentarios de `webBuilder.UseStartup<StartupSCreX>();` en *Program.cs*.

## <a name="disable-status-code-pages"></a>Deshabilitar las páginas de códigos de estado

Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Código de control de excepciones

El código de las páginas de control de excepciones también puede iniciar excepciones. Las páginas de errores de producción se deben probar minuciosamente y se debe tener especial cuidado para evitar que inicien sus propias excepciones.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Encabezados de respuesta

Una vez enviados los encabezados de una respuesta:

* La aplicación no puede cambiar el código de estado de la respuesta.
* No se pueden ejecutar páginas o controladores de excepciones. Deberá completarse la respuesta o anularse la conexión.

## <a name="server-exception-handling"></a>Control de excepciones del servidor

Además de la lógica de control de excepciones de la aplicación, la [implementación del servidor HTTP](xref:fundamentals/servers/index) puede controlar algunas excepciones. Si el servidor almacena en caché una excepción antes de que se envíen los encabezados de respuesta, envía una respuesta `500 - Internal Server Error` sin cuerpo. Si el servidor almacena en caché una excepción después de que se envían los encabezados de respuesta, cierra la conexión. El servidor controla las solicitudes que no controla la aplicación. El control de excepciones del servidor controla cualquier excepción que se produzca cuando el servidor controle la solicitud. Las páginas de error personalizadas, el middleware de control de excepciones y los filtros de la aplicación no afectan este comportamiento.

## <a name="startup-exception-handling"></a>Control de excepciones de inicio

Solo el nivel de hospedaje puede controlar las excepciones que tienen lugar durante el inicio de la aplicación. El host puede configurarse para [capturar errores de inicio](xref:fundamentals/host/web-host#capture-startup-errors) y [capturar errores detallados](xref:fundamentals/host/web-host#detailed-errors).

La capa de hospedaje puede mostrar una página de error para un error de inicio capturado solo si este se produce después del enlace de puerto/dirección del host. Si se produce un error de enlace:

* La capa de hospedaje registra una excepción crítica.
* El proceso de dotnet se bloquea.
* No se muestra ninguna página de error si el servidor HTTP es [Kestrel](xref:fundamentals/servers/kestrel).

Si se ejecuta en [IIS](/iis), en Azure App Service o en [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) devuelve un *error de proceso 502.5* si el proceso no se puede iniciar. Para obtener más información, vea <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Página de error de la base de datos

El filtro de excepciones `AddDatabaseDeveloperPageExceptionFilter` de la página del desarrollador de bases de datos captura las excepciones relacionadas con la base de datos que se pueden resolver mediante migraciones de Entity Framework Core. Cuando se producen estas excepciones, se genera una respuesta HTML con los detalles de las acciones posibles para resolver el problema. Esta página debe habilitarse solo en el entorno de desarrollo. El siguiente código fue generado por las plantillas de Razor Pages de ASP.NET Core cuando se especificaron cuentas de usuario individuales:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Filtros de excepciones

En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción. En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página. Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro. Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.

Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el [middleware de control de excepciones](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) integrado, `UseExceptionHandler`. Se recomienda usar `UseExceptionHandler`, a no ser que tenga que realizar el control de errores de otra forma según la acción de MVC elegida.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Errores de estado del modelo

Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

De [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)

Este artículo trata sobre los métodos comunes para controlar errores en aplicaciones web ASP.NET Core. Consulte <xref:web-api/handle-errors> para las API web.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="developer-exception-page"></a>Página de excepciones para el desarrollador

En la *Página de excepciones para el desarrollador* se muestra información detallada sobre las excepciones de la solicitud. Las plantillas de ASP.NET Core generan el siguiente código:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

El código anterior habilita la página de excepciones para el desarrollador cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments).

Las plantillas colocan <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> antes que cualquier middleware para que las excepciones se detecten en el middleware que sigue.

El código anterior solo habilita la página de excepciones para el desarrollador **cuando la aplicación se ejecuta en el entorno de desarrollo**. La información detallada de la excepción no debe mostrarse públicamente cuando la aplicación se ejecuta en producción. Para más información sobre la configuración de entornos, consulte <xref:fundamentals/environments>.

La página de excepciones para el desarrollador incluye la siguiente información sobre la excepción y la solicitud:

* Seguimiento de la pila
* Parámetros de cadena de consulta (si existen)
* Cookie (si existen)
* encabezados

## <a name="exception-handler-page"></a>Página del controlador de excepciones

Para configurar una página de control de errores personalizada para el entorno de producción, use el middleware de control de excepciones. El middleware:

* Captura y registra las excepciones.
* Vuelve a ejecutar la solicitud en una canalización alternativa correspondiente a la página o el controlador indicados. La solicitud no se vuelve a ejecutar si se ha iniciado la respuesta. El código generado por la plantilla vuelve a ejecutar la solicitud para `/Error`.

En el ejemplo siguiente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> agrega middleware de control de excepciones en entornos que no son de desarrollo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

La plantilla de aplicación de Razor Pages proporciona una página de error ( *.cshtml* ) y una clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) en la carpeta *Pages*. Para una aplicación de MVC, la plantilla de proyecto incluye un método de acción para el error y una vista del error.

No marque el método de acción del controlador de errores con atributos de método HTTP, como `HttpGet`. Los verbos explícitos impiden que algunas solicitudes lleguen al método. Permita el acceso anónimo al método si los usuarios no autenticados deben recibir la vista del error.

### <a name="access-the-exception"></a>Acceso a la excepción

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acceder a la ruta de acceso a la solicitud original y a la excepción en una página o un controlador de errores:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **No** proporcione información de errores confidencial a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para desencadenar la página de control de excepciones anterior, establezca el entorno en producciones y fuerce una excepción.

## <a name="exception-handler-lambda"></a>Lambda del controlador de excepciones

Una alternativa a una [página del controlador de excepciones personalizada](#exception-handler-page) es proporcionar una expresión lambda en <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Usar una expresión lambda permite acceder al error antes de devolver la respuesta.

Este es un ejemplo del uso de una expresión lambda para el control de excepciones:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

En el código anterior, se agrega `await context.Response.WriteAsync(new string(' ', 512));` para que el explorador Internet Explorer muestre el mensaje de error en lugar de un mensaje de error de IE. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **No** proporcione información de errores confidencial de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> a los clientes. Proporcionar información de los errores es un riesgo para la seguridad.

Para ver el resultado de la expresión lambda de control de excepciones en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use las directivas de preprocesador `ProdEnvironment` y `ErrorHandlerLambda` y, después, seleccione **Trigger an exception** (Desencadenar una excepción) en la página principal.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Una aplicación ASP.NET Core no proporciona de forma predeterminada una página de códigos de estado para los códigos de estado HTTP, como *404 - No encontrado*. La aplicación devuelve un código de estado y un cuerpo de respuesta vacío. Para proporcionar páginas de códigos de estado, use el middleware de páginas de códigos de estado.

El middleware está disponible en el paquete [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/).

Para habilitar los controladores de solo texto predeterminados para los códigos de estado de error comunes, llame a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> en el método `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Llame a `UseStatusCodePages` antes del middleware de control de solicitudes (por ejemplo, middleware de archivos estáticos y middleware de MVC).

Cuando no se usa `UseStatusCodePages`, al navegar a una dirección URL sin punto de conexión se devuelve un mensaje de error dependiente del explorador que indica que no se encuentra el punto de conexión. Por ejemplo, al navegar a `Home/Privacy2`. Cuando se llama a `UseStatusCodePages`, el explorador devuelve:

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages con cadena de formato

Para personalizar el texto y el tipo de contenido de la respuesta, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una cadena de tipo de contenido y formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages con una expresión lambda

Para especificar el código de escritura de respuesta y control de errores personalizado, use la sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que adopta una expresión lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Envía un código de estado *302 - Encontrado* al cliente.
* Redirige al cliente a la ubicación proporcionada en la plantilla de dirección URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

La plantilla de dirección URL puede incluir un marcador de posición `{0}` para el código de estado, como se muestra en el ejemplo. Si la plantilla de dirección URL comienza con `~` (tilde), la `~` se sustituye por el elemento `PathBase` de la aplicación. Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión. Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método se usa normalmente cuando la aplicación:

* Debe redirigir al cliente a un punto de conexión diferente, normalmente en casos en los que una aplicación diferente procesa el error. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión redirigido.
* No debe conservar ni devolver el código de estado original con la respuesta de redirección inicial.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Método de extensión <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Devuelve el código de estado original al cliente.
* Genera el cuerpo de respuesta, para lo cual vuelve a ejecutar la canalización de solicitud mediante una ruta de acceso alternativa.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Si apunta a un punto de conexión de dentro de la aplicación, cree una vista de MVC o una página de Razor para ese punto de conexión. Asegúrese de que `UseStatusCodePagesWithReExecute` se coloca antes de `UseRouting` para que la solicitud se pueda volver a enrutar a la página de estado. Para obtener un ejemplo de Razor Pages, vea *Pages/StatusCode.cshtml* en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método se usa normalmente cuando la aplicación debe:

* Procesar la solicitud sin redirigirla a un punto de conexión diferente. En el caso de aplicaciones web, la barra de direcciones del explorador del cliente refleja el punto de conexión solicitado originalmente.
* Conservar y devolver el código de estado original con la respuesta.

Las plantillas de dirección URL y cadena de consulta pueden incluir un marcador de posición (`{0}`) relativo al código de estado. La plantilla de dirección URL debe empezar con una barra diagonal (`/`). Cuando se use un marcador de posición en la ruta de acceso, confirme que el punto de conexión (página o controlador) puede procesar el segmento de línea. Por ejemplo, una página de Razor de errores debe aceptar el valor de segmento de línea opcional con la directiva `@page`:

```cshtml
@page "{code?}"
```

El punto de conexión que procesa el error puede obtener la dirección URL original que generó el error, como se muestra en el ejemplo siguiente:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Deshabilitar las páginas de códigos de estado

Para deshabilitar las páginas de códigos de estado de un método de acción o controlador MVC, use el atributo [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Para deshabilitar las páginas de códigos de estado de solicitudes específicas de un método de controlador de Razor Pages o un controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

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

En las aplicaciones de MVC, los filtros de excepciones se pueden configurar globalmente, o bien por controlador o por acción. En las aplicaciones de Razor Pages, se pueden configurar a nivel global o por modelo de página. Estos filtros controlan todas las excepciones no controladas que se hayan producido durante la ejecución de una acción de controlador o de otro filtro. Para obtener más información, vea <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Los filtros de excepciones son útiles para interceptar las excepciones que se producen en las acciones de MVC, pero no son tan flexibles como el middleware de control de excepciones. Se recomienda usar el middleware. Use filtros únicamente cuando deba realizar el control de errores de manera diferente según la acción de MVC elegida.

## <a name="model-state-errors"></a>Errores de estado del modelo

Para obtener información sobre cómo controlar los errores de estado de los modelos, vea [Enlace de modelos](xref:mvc/models/model-binding) y [Validación de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
