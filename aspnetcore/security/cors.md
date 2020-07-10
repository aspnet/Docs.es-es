---
title: Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo CORS como estándar para permitir o rechazar solicitudes entre orígenes en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: dc8e68ef482025443147eeb27bd3f245c1a1a5ed
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212888"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)

En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.

La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web. Esta restricción se denomina *directiva de mismo origen*. La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio. En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación. Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):

* Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.
* **No** es una característica de seguridad, CORS reduce la seguridad. Una API no es más segura al permitir CORS. Para obtener más información, vea [Cómo funciona CORS](#how-cors).
* Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.
* Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mismo origen

Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estas dos direcciones URL tienen el mismo origen:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:

* `https://example.net`: Dominio diferente
* `https://www.example.com/foo.html`: Subdominio diferente
* `http://example.com/foo.html`: Esquema diferente
* `https://example.com:9000/foo.html`: Puerto diferente

## <a name="enable-cors"></a>Habilitación de CORS

Hay tres maneras de habilitar CORS:

* En middleware con una directiva [con nombre](#np) o una [directiva predeterminada](#dp).
* Usar el [enrutamiento de puntos de conexión](#ecors).
* Con el atributo [[EnableCors]](#attr) .

El uso del atributo [[EnableCors]](#attr) con una directiva con nombre proporciona el control más fino para limitar los puntos de conexión que admiten CORS.

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A>se debe llamar a antes de <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> usar `UseResponseCaching` .

Cada enfoque se detalla en las secciones siguientes.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS con Directiva con nombre y middleware

Middleware de CORS controla las solicitudes entre orígenes. En el código siguiente se aplica una directiva de CORS a todos los puntos de conexión de la aplicación con los orígenes especificados:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

El código anterior:

* Establece el nombre de la Directiva en `_myAllowSpecificOrigins` . El nombre de la Directiva es arbitrario.
* Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión y especifica la `_myAllowSpecificOrigins` Directiva CORS. `UseCors`agrega el middleware de CORS. La llamada a `UseCors` debe colocarse después `UseRouting` de, pero antes de `UseAuthorization` . Para obtener más información, vea [orden de middleware](xref:fundamentals/middleware/index#middleware-order).
* Llama a <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). La expresión lambda toma un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto. [Las opciones de configuración](#cors-policy-options), como `WithOrigins` , se describen más adelante en este artículo.
* Habilita la `_myAllowSpecificOrigins` Directiva CORS para todos los puntos de conexión del controlador. Consulte [enrutamiento de puntos de conexión](#ecors) para aplicar una directiva de CORS a puntos de conexión específicos.
* Al usar el [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware), llame a antes de <xref:Owin.CorsExtensions.UseCors%2A> <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .

Con el enrutamiento de punto de conexión, el middleware de CORS **debe** estar configurado para ejecutarse entre las llamadas a `UseRouting` y `UseEndpoints` .

Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.

Los <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos se pueden encadenar, tal y como se muestra en el código siguiente:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Nota: la dirección URL especificada **no** debe contener una barra diagonal final ( `/` ). Si la dirección URL termina con `/` , la comparación devuelve `false` y no se devuelve ningún encabezado.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS con Directiva y middleware predeterminados

El siguiente código resaltado habilita la directiva predeterminada CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

El código anterior aplica la directiva predeterminada CORS a todos los puntos de conexión del controlador.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Habilitación de CORS con enrutamiento de punto de conexión

La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite [solicitudes preparatorias automáticas](#apf). Para obtener más información, consulte [este problema de github](https://github.com/dotnet/aspnetcore/issues/20709) y [pruebe CORS con enrutamiento de puntos de conexión y [HttpOptions]](#tcer).

Con el enrutamiento de punto de conexión, CORS se puede habilitar en cada punto de conexión mediante el <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> conjunto de métodos de extensión:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

En el código anterior:

* `app.UseCors`habilita el middleware de CORS. Dado que no se ha configurado una directiva predeterminada, `app.UseCors()` solo no habilita CORS.
* Los `/echo` puntos de conexión del controlador y permiten solicitudes entre orígenes mediante la Directiva especificada.
* Los `/echo2` extremos de las Razor páginas y **no** permiten solicitudes entre orígenes porque no se especificó ninguna directiva predeterminada.

El atributo [[DisableCors]](#dc) **no** deshabilita CORS habilitado por el enrutamiento de puntos de conexión con `RequireCors` .

Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar código similar al anterior.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Habilitación de CORS con atributos

La habilitación de CORS con el atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) y la aplicación de una directiva con nombre solo a los puntos de conexión que requieren CORS proporciona el control más fino.

El atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente. El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos:

* `[EnableCors]`Especifica la directiva predeterminada.
* `[EnableCors("{Policy String}")]`especifica una directiva con nombre.

El `[EnableCors]` atributo se puede aplicar a:

* RazorDel`PageModel`
* Controller
* Método de acción del controlador

Se pueden aplicar directivas diferentes a los controladores, modelos de página o métodos de acción con el `[EnableCors]` atributo. Cuando el `[EnableCors]` atributo se aplica a un controlador, un modelo de página o un método de acción y CORS está habilitado en middleware, se aplican **ambas** directivas. **Se recomienda no combinar directivas. Use el** `[EnableCors]` **atributo o middleware, no ambos en la misma aplicación.**

En el código siguiente se aplica una directiva diferente a cada método:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

En el código siguiente se crean dos directivas de CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Para el control más fino de la limitación de solicitudes de CORS:

* Se usa `[EnableCors("MyPolicy")]` con una directiva con nombre.
* No defina una directiva predeterminada.
* No use el [enrutamiento de puntos de conexión](#ecors).

El código de la sección siguiente se ajusta a la lista anterior.

Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.

<a name="dc"></a>

### <a name="disable-cors"></a>Deshabilitar CORS

El atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **no** deshabilita CORS habilitado por el enrutamiento de [puntos de conexión](#ecors).

En el código siguiente se define la Directiva CORS `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

El código siguiente deshabilita CORS para la `GetValues2` acción:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

El código anterior:

* No habilita CORS con [enrutamiento de punto de conexión](#ecors).
* No define una [directiva predeterminada de CORS](#dp).
* Usa [[EnableCors ("MyPolicy")]](#attr) para habilitar la `"MyPolicy"` Directiva CORS para el controlador.
* Deshabilita CORS para el `GetValues2` método.

Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar el código anterior.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opciones de directiva de CORS

En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:

* [Establecer los orígenes permitidos](#set-the-allowed-origins)
* [Establecer los métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Establecer los encabezados de solicitud permitidos](#set-the-allowed-request-headers)
* [Establecer los encabezados de respuesta expuestos](#set-the-exposed-response-headers)
* [Credenciales en solicitudes entre orígenes](#credentials-in-cross-origin-requests)
* [Establecer la hora de expiración de la comprobación previa](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama a en `Startup.ConfigureServices` . Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.

## <a name="set-the-allowed-origins"></a>Establecer los orígenes permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitudes de CORS de todos los orígenes con cualquier esquema ( `http` o `https` ). `AllowAnyOrigin`no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.

> [!NOTE]
> Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios. El servicio CORS devuelve una respuesta de CORS no válida cuando se configura una aplicación con ambos métodos.

`AllowAnyOrigin`afecta a las solicitudes preparatorias y al `Access-Control-Allow-Origin` encabezado. Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Establecer los métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite cualquier método HTTP:
* Afecta a las solicitudes preparatorias y al `Access-Control-Allow-Methods` encabezado. Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Establecer los encabezados de solicitud permitidos

Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados [encabezados de solicitud de autor](https://xhr.spec.whatwg.org/#request), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> y especifique los encabezados permitidos:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos los [encabezados de solicitud de autor](https://www.w3.org/TR/cors/#author-request-headers), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`afecta a las solicitudes preparatorias y al encabezado [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

Una coincidencia de directiva de middleware de CORS con encabezados específicos especificados por `WithHeaders` solo es posible cuando los encabezados enviados `Access-Control-Request-Headers` coinciden exactamente con los encabezados indicados en `WithHeaders` .

Por ejemplo, considere una aplicación configurada de la siguiente manera:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Middleware de CORS rechaza una solicitud preparatoria con el siguiente encabezado de solicitud porque `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) no aparece en `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

La aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes.

### <a name="set-the-exposed-response-headers"></a>Establecer los encabezados de respuesta expuestos

De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación. Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).

Los encabezados de respuesta que están disponibles de forma predeterminada son:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La especificación CORS llama a estos encabezados de *respuesta simple*. Para que otros encabezados estén disponibles para la aplicación, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Credenciales en solicitudes entre orígenes

Las credenciales requieren un tratamiento especial en una solicitud de CORS. De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes. Las credenciales incluyen cookies y esquemas de autenticación HTTP. Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true` .

Usar `XMLHttpRequest` directamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usar jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

El servidor debe permitir las credenciales. Para permitir las credenciales entre orígenes, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.

Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.

Permitir las credenciales entre orígenes es un riesgo para la seguridad. Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La especificación CORS también indica que el establecimiento de orígenes en `"*"` (todos los orígenes) no es válido si el `Access-Control-Allow-Credentials` encabezado está presente.

<a name="pref"></a>

## <a name="preflight-requests"></a>Solicitudes preparatorias

En algunas solicitudes de CORS, el explorador envía una solicitud [Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de efectuar la solicitud real. Esta solicitud se denomina [solicitud preparatoria](https://developer.mozilla.org/docs/Glossary/Preflight_request). El explorador puede omitir la solicitud preparatoria si se cumplen **todas** las condiciones siguientes:

* El método de solicitud es GET, HEAD o POST.
* La aplicación no establece encabezados de solicitud distintos de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .
* El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la aplicación mediante una llamada a `setRequestHeader` en el `XMLHttpRequest` objeto. La especificación CORS llama a estos encabezados de [solicitud Author](https://www.w3.org/TR/cors/#author-request-headers). La regla no se aplica a los encabezados que el explorador puede establecer, como `User-Agent` , `Host` o `Content-Length` .

A continuación se incluye una respuesta de ejemplo similar a la solicitud preparatoria realizada desde el botón **[Put Test]** de la sección [Test CORS](#testc) de este documento.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

La solicitud preparatoria usa el método [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Puede incluir los encabezados siguientes:

* [Access-Control-request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): método HTTP que se utilizará para la solicitud real.
* [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): una lista de encabezados de solicitud que la aplicación establece en la solicitud real. Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent` .
* [Access-Control-Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Si se deniega la solicitud preparatoria, la aplicación devuelve una `200 OK` respuesta pero no establece los encabezados CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes. Para obtener un ejemplo de una solicitud preparatoria denegada, consulte la sección [prueba CORS](#testc) de este documento.

Con las herramientas F12, la aplicación de consola muestra un error similar a uno de los siguientes, en función del explorador:

* Firefox: solicitud entre orígenes bloqueada: la misma directiva de origen no permite la lectura del recurso remoto en `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Motivo: la solicitud de CORS no se realizó correctamente). [Más información](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Basado en cromo: el acceso a la captura en ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' desde el origen ' ' se ha https://cors3.azurewebsites.net bloqueado mediante la Directiva de CORS: la respuesta a la solicitud preparatoria no pasa la comprobación de control de acceso: no hay ningún encabezado ' Access-Control-Allow-Origin ' presente en el recurso solicitado. Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.

Para permitir encabezados específicos, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Para permitir todos los [encabezados de solicitud de autor](https://www.w3.org/TR/cors/#author-request-headers), llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Los exploradores no son coherentes en el modo en que se establecen `Access-Control-Request-Headers` . Si:

* Los encabezados se establecen en un valor distinto de`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se llama a: incluye al menos `Accept` , `Content-Type` y `Origin` , además de los encabezados personalizados que desee admitir.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Código de solicitud de comprobación previa automática

Cuando se aplica la Directiva CORS:

* Globalmente llamando a `app.UseCors` en `Startup.Configure` .
* Usar el `[EnableCors]` atributo.

ASP.NET Core responde a la solicitud de opciones preparatorias.

La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite solicitudes preparatorias automáticas.

En la sección [prueba CORS](#testc) de este documento se muestra este comportamiento.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Atributo [HttpOptions] para solicitudes preparatorias

Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS. En algunos escenarios, puede que este no sea el caso. Por ejemplo, con [CORS con enrutamiento de punto de conexión](#ecors).

En el código siguiente se usa el atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para crear extremos para las solicitudes Options:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar el código anterior.

### <a name="set-the-preflight-expiration-time"></a>Establecer la hora de expiración de la comprobación previa

El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria. Para establecer este encabezado, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Cómo funciona CORS

En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.

* CORS **no** es una característica de seguridad. CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.
  * Por ejemplo, un actor malintencionado podría usar el [scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.
* Una API no es más segura, ya que permite CORS.
  * Es el cliente (explorador) el que debe aplicar CORS. El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta. Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient de .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un explorador web escribiendo la dirección URL en la barra de direcciones.
* Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.
  * Los exploradores sin CORS no pueden realizar solicitudes entre orígenes. Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción. JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta. Los scripts pueden cargarse entre orígenes.

La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes. Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes. No es necesario el código personalizado de JavaScript para habilitar CORS.

[Botón poner prueba](https://cors3.azurewebsites.net/test) en el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implementado

A continuación se presenta un ejemplo de una solicitud entre orígenes desde el botón de prueba [valores](https://cors3.azurewebsites.net/) a `https://cors1.azurewebsites.net/api/values` . El `Origin` encabezado:

* Proporciona el dominio del sitio que realiza la solicitud.
* Es obligatorio y debe ser diferente del host.

**Encabezados generales**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Encabezados de respuesta**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Encabezados de solicitud**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

En `OPTIONS` las solicitudes, el servidor establece **Response headers** el `Access-Control-Allow-Origin: {allowed origin}` encabezado de encabezados de respuesta en la respuesta. Por ejemplo, la solicitud de botón [eliminar [EnableCors]](https://cors1.azurewebsites.net/test?number=2) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado `OPTIONS` contiene los encabezados siguientes:

**Encabezados generales**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Encabezados de respuesta**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Encabezados de solicitud**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

En los **encabezados de respuesta**anteriores, el servidor establece el encabezado [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) en la respuesta. El `https://cors1.azurewebsites.net` valor de este encabezado coincide con el `Origin` encabezado de la solicitud.

Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> se llama a, `Access-Control-Allow-Origin: *` se devuelve el valor del carácter comodín. `AllowAnyOrigin`permite cualquier origen.

Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado. En concreto, el explorador no permite la solicitud. Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.

<a name="options"></a>

### <a name="display-options-requests"></a>Mostrar solicitudes de opciones

De forma predeterminada, los exploradores Chrome y Edge no muestran las solicitudes de opciones en la pestaña red de las herramientas F12. Para mostrar las solicitudes OPTIONs en estos exploradores:

* `chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`
* deshabilite la marca.
* Reiniciar.

Firefox muestra las solicitudes de opciones de forma predeterminada.

## <a name="cors-in-iis"></a>CORS en IIS

Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo. Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.

<a name="testc"></a>

## <a name="test-cors"></a>Prueba de CORS

La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tiene código para probar CORS. Vea [cómo descargarlo](xref:index#how-to-download-a-sample). El ejemplo es un proyecto de API con Razor páginas agregadas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

A continuación se `ValuesController` proporcionan los puntos de conexión para las pruebas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) lo proporciona el paquete NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) y muestra información de ruta.

Pruebe el código de ejemplo anterior utilizando uno de los métodos siguientes:

* Use la aplicación de ejemplo implementada en [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . No es necesario descargar el ejemplo.
* Ejecute el ejemplo con `dotnet run` el uso de la dirección URL predeterminada de `https://localhost:5001` .
* Ejecute el ejemplo desde Visual Studio con el puerto establecido en 44398 para una dirección URL de `https://localhost:44398` .

Usar un explorador con las herramientas F12:

* Seleccione el botón **valores** y revise los encabezados en la pestaña **red** .
* Seleccione el botón **poner prueba** . Consulte [solicitudes de visualización de opciones](#options) para obtener instrucciones sobre cómo mostrar la solicitud Options. La **prueba Put** crea dos solicitudes, una solicitud preparatoria de opciones y la solicitud Put.
* Seleccione el **`GetValues2 [DisableCors]`** botón para desencadenar una solicitud de CORS con errores. Como se mencionó en el documento, la respuesta devuelve 200 Success, pero no se realiza la solicitud de CORS. Seleccione la pestaña **consola** para ver el error de CORS. Dependiendo del explorador, se muestra un error similar al siguiente:

     `'https://cors1.azurewebsites.net/api/values/GetValues2'`La Directiva de CORS bloqueó el acceso a la captura desde el origen `'https://cors3.azurewebsites.net'` : no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado. Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.
     
Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [rizo](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/). Cuando se usa una herramienta, el origen de la solicitud especificada por el `Origin` encabezado debe ser diferente del host que recibe la solicitud. Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:

* No es necesario que el middleware de CORS procese la solicitud.
* Los encabezados CORS no se devuelven en la respuesta.

El siguiente comando usa `curl` para emitir una solicitud Options con información:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]

La habilitación de CORS en cada punto de conexión mediante `RequireCors` actualmente **no** admite [solicitudes preparatorias automáticas](#apf). Considere el código siguiente, que usa el [enrutamiento de punto de conexión para habilitar CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

A continuación se `TodoItems1Controller` proporcionan los puntos de conexión para las pruebas:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=1) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado.

Los botones **Delete [EnableCors]** y **Get [EnableCors]** se ejecutan correctamente, ya que los extremos tienen `[EnableCors]` y responden a las solicitudes preparatorias. Se produce un error en los otros puntos de conexión. Se produce un error en el botón **Get** porque [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envía:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Lo siguiente `TodoItems2Controller` proporciona puntos de conexión similares, pero incluye código explícito para responder a las solicitudes de opciones:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=2) del ejemplo implementado. En la lista desplegable **controlador** , seleccione **preparatoria** y después **establezca controlador**. Todas las llamadas a CORS a los `TodoItems2Controller` puntos de conexión se realizan correctamente.

## <a name="additional-resources"></a>Recursos adicionales

* [Uso compartido de recursos entre orígenes (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introducción al módulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.

La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web. Esta restricción se denomina *directiva de mismo origen*. La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio. En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación. Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):

* Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.
* **No** es una característica de seguridad, CORS reduce la seguridad. Una API no es más segura al permitir CORS. Para obtener más información, vea [Cómo funciona CORS](#how-cors).
* Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.
* Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mismo origen

Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Estas dos direcciones URL tienen el mismo origen:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:

* `https://example.net`: Dominio diferente
* `https://www.example.com/foo.html`: Subdominio diferente
* `http://example.com/foo.html`: Esquema diferente
* `https://example.com:9000/foo.html`: Puerto diferente

Internet Explorer no tiene en cuenta el puerto al comparar los orígenes.

## <a name="cors-with-named-policy-and-middleware"></a>CORS con Directiva con nombre y middleware

Middleware de CORS controla las solicitudes entre orígenes. El código siguiente habilita CORS para toda la aplicación con el origen especificado:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

El código anterior:

* Establece el nombre de la Directiva en " \_ myAllowSpecificOrigins". El nombre de la Directiva es arbitrario.
* Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión, que habilita CORS.
* Llama a <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). La expresión lambda toma un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto. [Las opciones de configuración](#cors-policy-options), como `WithOrigins` , se describen más adelante en este artículo.

La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.

El <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método puede encadenar métodos, tal y como se muestra en el código siguiente:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Nota: la dirección URL **no** debe contener una barra diagonal final ( `/` ). Si la dirección URL termina con `/` , la comparación devuelve `false` y no se devuelve ningún encabezado.

El código siguiente aplica las directivas de CORS a todos los puntos de conexión de aplicaciones a través del middleware CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Nota: `UseCors` se debe llamar a antes de `UseMvc` .

Consulte [habilitación de CORS en Razor páginas, controladores y métodos de acción](#ecors) para aplicar la Directiva CORS en el nivel de página/controlador/acción.

Consulte [prueba de CORS](#test) para obtener instrucciones sobre cómo probar código similar al código anterior.

## <a name="enable-cors-with-attributes"></a>Habilitación de CORS con atributos

El atributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente. El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos.

`[EnableCors]`Se usa para especificar la directiva predeterminada y `[EnableCors("{Policy String}")]` para especificar una directiva.

El `[EnableCors]` atributo se puede aplicar a:

* RazorDel`PageModel`
* Controller
* Método de acción del controlador

Puede aplicar diferentes directivas al controlador/página-modelo o acción con el `[EnableCors]` atributo. Cuando el `[EnableCors]` atributo se aplica a un método de acción/modelo de página/controladores y CORS está habilitado en middleware, se aplican **ambas** directivas. Se recomienda **no** combinar directivas. Use el `[EnableCors]` atributo o middleware, **no ambos**. Al usar `[EnableCors]` , **no** defina una directiva predeterminada.

En el código siguiente se aplica una directiva diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

En el código siguiente se crea una directiva predeterminada de CORS y una directiva denominada `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Deshabilitar CORS

El atributo [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deshabilita CORS para el controlador/página-modelo/acción.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opciones de directiva de CORS

En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:

* [Establecer los orígenes permitidos](#set-the-allowed-origins)
* [Establecer los métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Establecer los encabezados de solicitud permitidos](#set-the-allowed-request-headers)
* [Establecer los encabezados de respuesta expuestos](#set-the-exposed-response-headers)
* [Credenciales en solicitudes entre orígenes](#credentials-in-cross-origin-requests)
* [Establecer la hora de expiración de la comprobación previa](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama a en `Startup.ConfigureServices` . Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.

## <a name="set-the-allowed-origins"></a>Establecer los orígenes permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Permite solicitudes de CORS de todos los orígenes con cualquier esquema ( `http` o `https` ). `AllowAnyOrigin`no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.

> [!NOTE]
> Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios. En el caso de una aplicación segura, especifique una lista exacta de orígenes si el cliente debe autorizarse para obtener acceso a los recursos del servidor.

`AllowAnyOrigin`afecta a las solicitudes preparatorias y al `Access-Control-Allow-Origin` encabezado. Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Establecer los métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite cualquier método HTTP:
* Afecta a las solicitudes preparatorias y al `Access-Control-Allow-Methods` encabezado. Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Establecer los encabezados de solicitud permitidos

Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados *encabezados de solicitud de autor*, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> y especifique los encabezados permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos los encabezados de solicitud de autor, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Esta configuración afecta a las solicitudes preparatorias y al `Access-Control-Request-Headers` encabezado. Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .

El middleware de CORS siempre permite `Access-Control-Request-Headers` que se envíen cuatro encabezados en el, independientemente de los valores configurados en CorsPolicy. Headers. Esta lista de encabezados incluye:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por ejemplo, considere una aplicación configurada de la siguiente manera:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware de CORS responde correctamente a una solicitud preparatoria con el siguiente encabezado de solicitud porque `Content-Language` siempre se permite:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Establecer los encabezados de respuesta expuestos

De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación. Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).

Los encabezados de respuesta que están disponibles de forma predeterminada son:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La especificación CORS llama a estos encabezados de *respuesta simple*. Para que otros encabezados estén disponibles para la aplicación, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciales en solicitudes entre orígenes

Las credenciales requieren un tratamiento especial en una solicitud de CORS. De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes. Las credenciales incluyen cookies y esquemas de autenticación HTTP. Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true` .

Usar `XMLHttpRequest` directamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usar jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

El servidor debe permitir las credenciales. Para permitir las credenciales entre orígenes, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.

Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.

Permitir las credenciales entre orígenes es un riesgo para la seguridad. Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La especificación CORS también indica que el establecimiento de orígenes en `"*"` (todos los orígenes) no es válido si el `Access-Control-Allow-Credentials` encabezado está presente.

### <a name="preflight-requests"></a>Solicitudes preparatorias

En algunas solicitudes de CORS, el explorador envía una solicitud adicional antes de efectuar la solicitud real. Esta solicitud se denomina *solicitud preparatoria*. El explorador puede omitir la solicitud preparatoria si se cumplen las condiciones siguientes:

* El método de solicitud es GET, HEAD o POST.
* La aplicación no establece encabezados de solicitud distintos de `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .
* El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la aplicación mediante una llamada a `setRequestHeader` en el `XMLHttpRequest` objeto. La especificación CORS llama a estos encabezados de *solicitud Author*. La regla no se aplica a los encabezados que el explorador puede establecer, como `User-Agent` , `Host` o `Content-Length` .

El siguiente es un ejemplo de una solicitud preparatoria:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La solicitud previa al vuelo usa el método HTTP OPTIONs. Incluye dos encabezados especiales:

* `Access-Control-Request-Method`: Método HTTP que se usará para la solicitud real.
* `Access-Control-Request-Headers`: Una lista de encabezados de solicitud que la aplicación establece en la solicitud real. Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS. Vea el [atributo [HttpOptions] para solicitudes preparatorias](#pro).

Una solicitud preparatoria de CORS podría incluir un `Access-Control-Request-Headers` encabezado, que indica al servidor los encabezados que se envían con la solicitud real.

Para permitir encabezados específicos, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos los encabezados de solicitud de autor, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Los exploradores no son totalmente coherentes en el modo en que se establecen `Access-Control-Request-Headers` . Si establece encabezados en un valor distinto de `"*"` (o use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), debe incluir al menos `Accept` , `Content-Type` y `Origin` , además de los encabezados personalizados que desee admitir.

A continuación se ofrece un ejemplo de respuesta a la solicitud preparatoria (siempre que el servidor permita la solicitud):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La respuesta incluye un `Access-Control-Allow-Methods` encabezado que enumera los métodos permitidos y, opcionalmente `Access-Control-Allow-Headers` , un encabezado, que enumera los encabezados permitidos. Si la solicitud preparatoria se realiza correctamente, el explorador envía la solicitud real.

Si se deniega la solicitud preparatoria, la aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS. Por lo tanto, el explorador no intenta la solicitud entre orígenes.

### <a name="set-the-preflight-expiration-time"></a>Establecer la hora de expiración de la comprobación previa

El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria. Para establecer este encabezado, llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Cómo funciona CORS

En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.

* CORS **no** es una característica de seguridad. CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.
  * Por ejemplo, un actor malintencionado podría usar [impedir el scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.
* La API no es más segura al permitir CORS.
  * Es el cliente (explorador) el que debe aplicar CORS. El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta. Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient de .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un explorador web escribiendo la dirección URL en la barra de direcciones.
* Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.
  * Los exploradores (sin CORS) no pueden realizar solicitudes entre orígenes. Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción. JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta. Los scripts pueden cargarse entre orígenes.

La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes. Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes. No es necesario el código personalizado de JavaScript para habilitar CORS.

A continuación se presenta un ejemplo de una solicitud entre orígenes. El `Origin` encabezado proporciona el dominio del sitio que realiza la solicitud. El `Origin` encabezado es obligatorio y debe ser diferente del host.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Si el servidor permite la solicitud, establece el `Access-Control-Allow-Origin` encabezado en la respuesta. El valor de este encabezado coincide con el `Origin` encabezado de la solicitud o con el valor comodín `"*"` , lo que significa que se permite cualquier origen:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado. En concreto, el explorador no permite la solicitud. Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.

<a name="test"></a>

## <a name="test-cors"></a>Prueba de CORS

Para probar CORS:

1. [Cree un proyecto de API](xref:tutorials/first-web-api). Como alternativa, puede [descargar el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite CORS con uno de los enfoques de este documento. Por ejemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Cree un proyecto de aplicación web ( Razor páginas o MVC). El ejemplo utiliza Razor páginas. Puede crear la aplicación web en la misma solución que el proyecto de API.
1. Agregue el siguiente código resaltado al archivo *index. cshtml* :

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. En el código anterior, reemplace `url: 'https://<web app>.azurewebsites.net/api/values/1',` por la dirección URL a la aplicación implementada.
1. Implemente el proyecto de API. Por ejemplo, [implemente en Azure](xref:host-and-deploy/azure-apps/index).
1. Ejecute las Razor páginas o la aplicación MVC desde el escritorio y haga clic en el botón **probar** . Use las herramientas F12 para revisar los mensajes de error.
1. Quite el origen localhost de `WithOrigins` e implemente la aplicación. Como alternativa, ejecute la aplicación cliente con un puerto diferente. Por ejemplo, ejecute desde Visual Studio.
1. Pruebe con la aplicación cliente. Los errores de CORS devuelven un error, pero el mensaje de error no está disponible para JavaScript. Use la pestaña consola de las herramientas F12 para ver el error. Dependiendo del explorador, obtendrá un error (en la consola de herramientas de F12) similar al siguiente:

   * Uso de Microsoft Edge:

     **SEC7120: [CORS] el origen `https://localhost:44375` no se encontró `https://localhost:44375` en el encabezado de respuesta Access-Control-Allow-Origin para el recurso entre orígenes en`https://webapi.azurewebsites.net/api/values/1`**

   * Usar Chrome:

     **`https://webapi.azurewebsites.net/api/values/1`La Directiva de CORS ha bloqueado el acceso a XMLHttpRequest desde el origen `https://localhost:44375` : no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado.**
     
Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/). Cuando se usa una herramienta, el origen de la solicitud especificada por el `Origin` encabezado debe ser diferente del host que recibe la solicitud. Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:

* No es necesario que el middleware de CORS procese la solicitud.
* Los encabezados CORS no se devuelven en la respuesta.

## <a name="cors-in-iis"></a>CORS en IIS

Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo. Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* [Uso compartido de recursos entre orígenes (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introducción al módulo IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
