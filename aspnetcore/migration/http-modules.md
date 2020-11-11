---
title: Migración de controladores y módulos HTTP a middleware de ASP.NET Core
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 4abba1d4304bf537bd96623527c851d9d15774a4
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508167"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migración de controladores y módulos HTTP a middleware de ASP.NET Core

En este artículo se muestra cómo migrar [módulos y controladores http de ASP.net existentes de System. WebServer](/iis/configuration/system.webserver/) a ASP.net Core [middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Módulos y controladores revisados

Antes de continuar con ASP.NET Core middleware, vamos a resumir primero cómo funcionan los módulos y controladores HTTP:

![Controlador de módulos](http-modules/_static/moduleshandlers.png)

**Los controladores son:**

* Clases que implementan [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Se utiliza para controlar las solicitudes con un nombre de archivo o una extensión determinados, como *. Report*

* [Configurado](/iis/configuration/system.webserver/handlers/) en *Web.config*

**Los módulos son:**

* Clases que implementan [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Se invoca para cada solicitud

* Capaz de cortocircuitar (detener el procesamiento de una solicitud)

* Puede agregarse a la respuesta HTTP o crear su propia

* [Configurado](/iis/configuration/system.webserver/modules/) en *Web.config*

**El orden en que los módulos procesan las solicitudes entrantes viene determinado por:**

1. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, Que es una serie de eventos desencadenados por ASP.net: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo puede crear un controlador para uno o más eventos.

2. Para el mismo evento, el orden en el que están configurados en *Web.config*.

Además de los módulos, puede agregar controladores para los eventos de ciclo de vida al archivo *global.asax.CS* . Estos controladores se ejecutan después de los controladores de los módulos configurados.

## <a name="from-handlers-and-modules-to-middleware"></a>De controladores y módulos a middleware

**El middleware es más sencillo que los módulos y controladores HTTP:**

* Los módulos, controladores, *global.asax.CS* , *Web.config* (excepto para la configuración de IIS) y el ciclo de vida de la aplicación han desaparecido

* Los roles de los módulos y controladores se han tomado por middleware

* El middleware se configura utilizando código en lugar de en *Web.config*

::: moniker range=">= aspnetcore-3.0"

* La [bifurcación de canalizaciones](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) permite enviar solicitudes a middleware específico, en función de no solo la dirección URL sino también de encabezados de solicitud, cadenas de consulta, etc.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* La [bifurcación de canalizaciones](xref:fundamentals/middleware/index#use-run-and-map) permite enviar solicitudes a middleware específico, en función de no solo la dirección URL sino también de encabezados de solicitud, cadenas de consulta, etc.

::: moniker-end

**El middleware es muy similar a los módulos:**

* Se invoca en principio para cada solicitud

* Es capaz de cortocircuitar una solicitud, [no pasar la solicitud al siguiente middleware](#http-modules-shortcircuiting-middleware) .

* Puede crear su propia respuesta HTTP

**El middleware y los módulos se procesan en un orden diferente:**

* El orden de middleware se basa en el orden en que se insertan en la canalización de solicitudes, mientras que el orden de los módulos se basa principalmente en <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> eventos

* El orden de las respuestas es el orden inverso al de las solicitudes, mientras que el orden de los módulos es el mismo para las solicitudes y las respuestas.

* Consulte [creación de una canalización de middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Software intermedio](http-modules/_static/middleware.png)

Tenga en cuenta que en la imagen anterior, el middleware de autenticación cortocircuitó la solicitud.

## <a name="migrating-module-code-to-middleware"></a>Migrar código de módulo a middleware

Un módulo HTTP existente tendrá un aspecto similar al siguiente:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Como se muestra en la página de [middleware](xref:fundamentals/middleware/index) , un middleware ASP.net Core es una clase que expone un `Invoke` método que toma un `HttpContext` y devuelve un `Task` . El nuevo middleware tendrá el siguiente aspecto:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

La plantilla de middleware anterior se tomó de la sección sobre [escritura de middleware](xref:fundamentals/middleware/write).

La clase auxiliar *MyMiddlewareExtensions* facilita la configuración del middleware en la `Startup` clase. El `UseMyMiddleware` método agrega la clase de middleware a la canalización de solicitudes. Los servicios que requiere el middleware se insertan en el constructor del middleware.

<a name="http-modules-shortcircuiting-middleware"></a>

El módulo puede finalizar una solicitud, por ejemplo, si el usuario no está autorizado:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Para controlar esto, un middleware no llama a `Invoke` en el siguiente middleware de la canalización. Tenga en cuenta que esto no finaliza completamente la solicitud, ya que los middleware anteriores se seguirán invocando cuando la respuesta vuelva a su camino a través de la canalización.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Al migrar la funcionalidad del módulo al nuevo middleware, es posible que el código no se compile porque la `HttpContext` clase ha cambiado significativamente en ASP.net Core. [Más adelante](#migrating-to-the-new-httpcontext), verá cómo migrar al nuevo ASP.net Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migración de la inserción de módulos en la canalización de solicitudes

Normalmente, los módulos HTTP se agregan a la canalización de solicitudes mediante *Web.config* :

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Convierta esto [agregando el nuevo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) a la canalización de solicitudes en la `Startup` clase:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

El punto exacto de la canalización en el que se inserta el middleware nuevo depende del evento que se administró como módulo ( `BeginRequest` , `EndRequest` , etc.) y su orden en la lista de módulos de *Web.config*.

Como se indicó anteriormente, no hay ningún ciclo de vida de la aplicación en ASP.NET Core y el orden en el que el middleware procesa las respuestas difiere del orden utilizado por los módulos. Esto podría hacer que su decisión de ordenación sea más desafiante.

Si la ordenación se convierte en un problema, puede dividir el módulo en varios componentes de middleware que se pueden ordenar de forma independiente.

## <a name="migrating-handler-code-to-middleware"></a>Migrar el código del controlador a middleware

Un controlador HTTP tiene un aspecto similar al siguiente:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

En el proyecto de ASP.NET Core, se traducirá a un middleware similar a este:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Este middleware es muy similar al middleware correspondiente a los módulos. La única diferencia real es que no hay ninguna llamada a `_next.Invoke(context)` . Esto tiene sentido, porque el controlador está al final de la canalización de solicitudes, por lo que no habrá ningún middleware siguiente que invocar.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrar la inserción del controlador a la canalización de solicitudes

La configuración de un controlador HTTP se realiza en *Web.config* y tiene un aspecto similar al siguiente:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Puede convertir esto agregando el nuevo middleware del controlador a la canalización de solicitudes en la `Startup` clase, de forma similar al middleware convertido desde módulos. El problema con este enfoque es que enviaría todas las solicitudes al nuevo middleware del controlador. Sin embargo, solo desea que las solicitudes con una extensión determinada lleguen a su middleware. Esto le proporcionaría la misma funcionalidad que tenía con el controlador HTTP.

Una solución consiste en bifurcar la canalización para las solicitudes con una extensión determinada, mediante el `MapWhen` método de extensión. Esto se realiza en el mismo método en el que `Configure` se agrega el otro middleware:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` toma estos parámetros:

1. Una expresión lambda que toma `HttpContext` y devuelve `true` si la solicitud debe desplazarse por la rama. Esto significa que puede crear una bifurcación de solicitudes no solo basándose en su extensión, sino también en encabezados de solicitud, parámetros de cadena de consulta, etc.

2. Una expresión lambda que toma un `IApplicationBuilder` y agrega todo el middleware para la rama. Esto significa que puede Agregar middleware adicional a la rama delante del middleware del controlador.

Middleware que se agrega a la canalización antes de que se invoque la rama en todas las solicitudes; la rama no tendrá ningún impacto en ellos.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Cargar opciones de middleware mediante el patrón de opciones

Algunos módulos y controladores tienen opciones de configuración que se almacenan en *Web.config*. Sin embargo, en ASP.NET Core se usa un nuevo modelo de configuración en lugar de *Web.config*.

El nuevo [sistema de configuración](xref:fundamentals/configuration/index) le proporciona estas opciones para solucionar este problemas:

* Inserte directamente las opciones en el middleware, tal como se muestra en la [sección siguiente](#loading-middleware-options-through-direct-injection).

* Use el [patrón de opciones](xref:fundamentals/configuration/options):

1. Cree una clase que contenga las opciones de middleware, por ejemplo:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Almacenar los valores de opción

   El sistema de configuración permite almacenar valores de opciones en cualquier lugar que desee. Sin embargo, la mayoría de los sitios usan *appsettings.json* , por lo que tomaremos este enfoque:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* aquí es un nombre de sección. No tiene que ser el mismo que el nombre de la clase de opciones.

3. Asociar los valores de opción a la clase Options

    El patrón de opciones usa el marco de inserción de dependencias de ASP.NET Core para asociar el tipo de opciones (como `MyMiddlewareOptions` ) a un `MyMiddlewareOptions` objeto que tiene las opciones reales.

    Actualice su `Startup` clase:

   1. Si está utilizando *appsettings.json* , agréguelo al generador de configuración en el `Startup` constructor:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Configurar el servicio de opciones:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Asocie las opciones a la clase de opciones:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Inserte las opciones en el constructor de middleware. Esto es similar a insertar opciones en un controlador.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   El método de extensión [UseMiddleware](#http-modules-usemiddleware) que agrega el middleware a se `IApplicationBuilder` encarga de la inserción de dependencias.

   Esto no se limita a los `IOptions` objetos. Cualquier otro objeto que requiera el middleware puede insertarse de esta manera.

## <a name="loading-middleware-options-through-direct-injection"></a>Carga de opciones de middleware mediante inyección directa

El patrón de opciones tiene la ventaja de que crea un acoplamiento flexible entre los valores de opciones y sus consumidores. Una vez que haya asociado una clase de opciones con los valores de las opciones reales, cualquier otra clase puede obtener acceso a las opciones a través del marco de inserción de dependencias. No es necesario pasar valores de opciones.

Esto se interrumpe si desea utilizar el mismo middleware dos veces, con distintas opciones. Por ejemplo, un middleware de autorización usado en distintas bifurcaciones, lo que permite diferentes roles. No se pueden asociar dos objetos de opciones diferentes a una clase de opciones.

La solución consiste en obtener los objetos de opciones con los valores de las opciones reales de la `Startup` clase y pasarlos directamente a cada instancia del middleware.

1. Agregue una segunda clave a *appsettings.json*

   Para agregar un segundo conjunto de opciones al *appsettings.json* archivo, use una nueva clave para identificarlo de forma exclusiva:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Recupera los valores de opciones y los pasa a middleware. El `Use...` método de extensión (que agrega el middleware a la canalización) es un lugar lógico para pasar los valores de opción: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Habilite el middleware para tomar un parámetro de opciones. Proporcione una sobrecarga del `Use...` método de extensión (que toma el parámetro Options y lo pasa a `UseMiddleware` ). Cuando `UseMiddleware` se llama a con parámetros, pasa los parámetros al constructor de middleware cuando crea una instancia del objeto middleware.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Observe cómo incluye el objeto de opciones en un `OptionsWrapper` objeto. Implementa `IOptions` , según lo esperado por el constructor de middleware.

## <a name="migrating-to-the-new-httpcontext"></a>Migración al nuevo HttpContext

Anteriormente, vio que el `Invoke` método en el middleware toma un parámetro de tipo `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` ha cambiado significativamente en ASP.NET Core. En esta sección se muestra cómo trasladar las propiedades usadas con más frecuencia de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) al nuevo `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**IDENTIFICADOR de solicitud único (no homólogo System. Web. HttpContext)**

Proporciona un identificador único para cada solicitud. Es muy útil incluir en los registros.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. request

**HttpContext. request. HttpMethod** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. request. QueryString** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. request. URL** y **HttpContext. request. RawUrl** se traducen a:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. request. IsSecureConnection** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. request. UserHostAddress** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. request. Cookie s** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. request. RequestContext. RouteData** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. request. Headers** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. request. UserAgent** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. request. UrlReferrer** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. request. ContentType** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. request. Form** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Lea los valores del formulario solo si el subtipo de contenido es *x-www-form-urlencoded* o *form-data*.

**HttpContext. request. InputStream** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Use este código únicamente en un middleware de tipo de controlador, al final de una canalización.
>
>Puede leer el cuerpo sin procesar como se muestra anteriormente solo una vez por solicitud. El middleware que intenta leer el cuerpo después de la primera lectura leerá un cuerpo vacío.
>
>Esto no se aplica a la lectura de un formulario como se mostró anteriormente, ya que se realiza desde un búfer.

### <a name="httpcontextresponse"></a>HttpContext. Response

**HttpContext. Response. status** y **HttpContext. Response. StatusDescription** se traducen a:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext. Response. ContentEncoding** y **HttpContext. Response. ContentType** se traducen a:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext. Response. ContentType** por sí mismo también se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** se convierte en:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

El servicio de un archivo se describe en <xref:fundamentals/request-features> .

**HttpContext. Response. Headers**

El envío de encabezados de respuesta es complicado por el hecho de que, si los establece después de haber escrito algo en el cuerpo de la respuesta, no se enviarán.

La solución consiste en establecer un método de devolución de llamada que se llamará justo antes de que se inicie la respuesta. Lo mejor es hacerlo al principio del `Invoke` método en el middleware. Es este método de devolución de llamada que establece los encabezados de respuesta.

El código siguiente establece un método de devolución de llamada llamado `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

El `SetHeaders` método de devolución de llamada tendría el siguiente aspecto:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie seg**

Cookiese desplaza al explorador en un encabezado *set- Cookie* Response. Como resultado, el envío cookie de s requiere la misma devolución de llamada que se usa para enviar encabezados de respuesta:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

El `SetCookies` método de devolución de llamada sería similar al siguiente:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Recursos adicionales

* [Información general sobre los controladores HTTP y los módulos HTTP](/iis/configuration/system.webserver/)
* [Configuración](xref:fundamentals/configuration/index)
* [Inicio de la aplicación](xref:fundamentals/startup)
* [Software intermedio](xref:fundamentals/middleware/index)
