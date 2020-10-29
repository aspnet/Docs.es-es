---
title: Enrutamiento en ASP.NET Core
author: rick-anderson
description: Descubra cómo el enrutamiento de ASP.NET Core es responsable de hacer coincidir las solicitudes HTTP, así como del envío a puntos de conexión ejecutables.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
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
uid: fundamentals/routing
ms.openlocfilehash: e3dd7168e6974f63fa963d3732bc5df41814c70e
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491630"
---
# <a name="routing-in-aspnet-core"></a>Enrutamiento en ASP.NET Core

Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

El enrutamiento es responsable de hacer coincidir las solicitudes HTTP entrantes y de enviarlas a los puntos de conexión ejecutables de la aplicación. Los [puntos de conexión](#endpoint) son las unidades de código de control de solicitudes ejecutable de la aplicación. Se definen en la aplicación y se configuran al iniciarla. El proceso de búsqueda de coincidencias de puntos de conexión puede extraer valores de la dirección URL de la solicitud y proporcionarlos para el procesamiento de la solicitud. Con la información de los puntos de conexión de la aplicación, el enrutamiento también puede generar direcciones URL que se asignan a los puntos de conexión.

Las aplicaciones pueden configurar el enrutamiento mediante:

- Controladores
- Razor Pages
- SignalR
- Servicios gRPC
- [Middleware](xref:fundamentals/middleware/index) habilitado para puntos de conexión, como las [comprobaciones de estado](xref:host-and-deploy/health-checks).
- Delegados y expresiones lambda registrados con el enrutamiento.

En este documento se describen los detalles de bajo nivel del enrutamiento de ASP.NET Core. Para obtener información sobre la configuración del enrutamiento:

* Para los controladores, vea <xref:mvc/controllers/routing>.
* Para las convenciones de Razor Pages, vea <xref:razor-pages/razor-pages-conventions>.

El sistema de enrutamiento de puntos de conexión descrito en este documento se aplica a ASP.NET Core 3.0 y versiones posteriores. Para obtener información sobre el sistema de enrutamiento anterior basado en <xref:Microsoft.AspNetCore.Routing.IRouter>, seleccione la versión ASP.NET Core 2.1 mediante uno de los enfoques siguientes:

* El selector de versión de una versión anterior.
* Seleccione [Enrutamiento de ASP.NET Core 2.1](?view=aspnetcore-2.1).

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Los ejemplos de descarga para este documento están habilitados por una clase `Startup` específica. Para ejecutar un ejemplo concreto, modifique *Program.cs* para llamar a la clase `Startup` deseada.

## <a name="routing-basics"></a>Fundamentos del enrutamiento

Todas las plantillas de ASP.NET Core incluyen el enrutamiento en el código generado. El enrutamiento se registra en la canalización de [middleware](xref:fundamentals/middleware/index) en `Startup.Configure`.

En el código siguiente se muestra un ejemplo básico de enrutamiento:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

El enrutamiento usa un par de middleware, registrado por <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> y <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:

* `UseRouting` agrega coincidencia de rutas a la canalización de middleware. Este middleware examina el conjunto de puntos de conexión definidos en la aplicación y selecciona la [mejor coincidencia](#urlm) en función de la solicitud.
* `UseEndpoints` agrega la ejecución del punto de conexión a la canalización de middleware. Ejecuta el delegado asociado al punto de conexión seleccionado.

En el ejemplo anterior se incluye un único punto de conexión de *ruta a código* a través del método [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*):

* Al enviar una solicitud HTTP `GET` a la dirección URL raíz `/`:
  * Se ejecuta el delegado de solicitud mostrado.
  * Se escribe `Hello World!` en la respuesta HTTP. De forma predeterminada, la dirección URL raíz `/` es `https://localhost:5001/`.
* Si el método de solicitud no es `GET` o la dirección URL raíz no es `/`, no se detecta ninguna ruta y se devuelve HTTP 404.

### <a name="endpoint"></a>punto de conexión

<a name="endpoint"></a>

El método `MapGet` se usa para definir un **punto de conexión** . Un punto de conexión es algo que se puede:

* Seleccionar, si se hacen coincidir la dirección URL y el método HTTP.
* Ejecutar, mediante la ejecución del delegado.

Los puntos de conexión que la aplicación puede ejecutar y hacer coincidir se configuran en `UseEndpoints`. Por ejemplo, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> y [métodos similares](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) conectan delegados de solicitud al sistema de enrutamiento.
Se pueden usar métodos adicionales para conectar características del marco ASP.NET Core al sistema de enrutamiento:
- [MapRazorPages para Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers para controladores](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> para SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> para gRPC](xref:grpc/aspnetcore)

En el ejemplo siguiente se muestra el enrutamiento con una plantilla de ruta más sofisticada:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

La cadena `/hello/{name:alpha}` es una **plantilla de ruta** . Se usa para configurar cómo se hace coincidir el punto de conexión. En este caso, la plantilla coincide con:

* Una dirección URL como `/hello/Ryan`.
* Cualquier ruta de dirección URL que comience por `/hello/`, seguido de una secuencia de caracteres alfabéticos.  `:alpha` aplica una restricción de ruta que solo coincide con caracteres alfabéticos. Las [restricciones de ruta](#route-constraint-reference) se explican más adelante en este documento.

El segundo segmento de la ruta de dirección URL, `{name:alpha}`:

* Está enlazado al parámetro `name`.
* Se captura y se almacena en [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

El sistema de enrutamiento de puntos de conexión descrito en este documento es nuevo desde ASP.NET Core 3.0. Sin embargo, todas las versiones de ASP.NET Core admiten el mismo conjunto de características de plantilla de ruta y restricciones de ruta.

En el ejemplo siguiente se muestra el enrutamiento con [comprobaciones de estado](xref:host-and-deploy/health-checks) y autorización:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

En el ejemplo anterior se muestra cómo:

* El middleware de autorización se puede usar con el enrutamiento.
* Los puntos de conexión se pueden usar para configurar el comportamiento de la autorización.

La llamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> agrega un punto de conexión de comprobación de estado. Al encadenar <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> a esta llamada, se adjunta una directiva de autorización al punto de conexión.

La llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> y <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> agrega el middleware de autenticación y autorización. Estos middleware se colocan entre <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> y `UseEndpoints` para que puedan:

* Vea qué punto de conexión ha seleccionado `UseRouting`.
* Aplique una directiva de autorización antes de que <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> envíe al punto de conexión.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadatos de punto de conexión

En el ejemplo anterior, hay dos puntos de conexión, pero solo el de comprobación de estado tiene una directiva de autorización adjunta. Si la solicitud coincide con el punto de conexión de comprobación de estado, `/healthz`, se realiza una comprobación de autorización. Esto demuestra que los puntos de conexión pueden tener datos adicionales adjuntos. Estos datos adicionales se denominan **metadatos** de punto de conexión:

* Los metadatos pueden ser procesados mediante middleware compatible con el enrutamiento.
* Los metadatos pueden ser de cualquier tipo de .NET.

## <a name="routing-concepts"></a>Conceptos de enrutamiento

El sistema de enrutamiento se basa en la canalización de middleware mediante la adición del eficaz concepto de **punto de conexión** . Los puntos de conexión representan unidades de la funcionalidad de la aplicación que son diferentes entre sí en cuanto al enrutamiento, la autorización y cualquier número de sistemas de ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Definición de punto de conexión de ASP.NET Core

Un punto de conexión de ASP.NET Core es:

* Ejecutable: tiene un objeto <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.
* Extensible: tiene una colección [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*).
* Selectable: opcionalmente, tiene [información de enrutamiento](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Enumerable: la colección de puntos de conexión se puede enumerar mediante la recuperación del objeto <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> de la [DI](xref:fundamentals/dependency-injection).

En el código siguiente se muestra cómo recuperar e inspeccionar el punto de conexión que coincide con la solicitud actual:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

El punto de conexión, si se selecciona, se puede recuperar de `HttpContext`. Se pueden inspeccionar sus propiedades. Los objetos de punto de conexión son inmutables y no se pueden modificar después de crearlos. El tipo más común de punto de conexión es <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>. `RouteEndpoint` incluye información que permite que el sistema de enrutamiento lo seleccione.

En el código anterior, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configura un [middleware](xref:fundamentals/middleware/index) insertado.

<a name="mt"></a>

En el código siguiente se muestra que, en función de dónde se llame a `app.Use` en la canalización, es posible que no haya un punto de conexión:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

En este ejemplo se agregan instrucciones `Console.WriteLine` que muestran si se ha seleccionado un punto de conexión o no. Para mayor claridad, en el ejemplo se asigna un nombre para mostrar al punto de conexión `/` proporcionado.

Al ejecutar este código con una dirección URL de `/` se muestra lo siguiente:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Al ejecutar este código con otra dirección URL se muestra lo siguiente:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Este resultado muestra que:

* El punto de conexión siempre es NULL antes de que se llame a `UseRouting`.
* Si se encuentra una coincidencia, el extremo no es NULL entre `UseRouting` y <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.
* El middleware `UseEndpoints` es **terminal** cuando se encuentra una coincidencia. El [middleware de terminal](#tm) se define más adelante en este documento.
* El middleware después de `UseEndpoints` solo se ejecuta cuando no se encuentra ninguna coincidencia.

El middleware `UseRouting` usa el método [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) para asociar el punto de conexión al contexto actual. Se puede reemplazar el middleware `UseRouting` con lógica personalizada y seguir aprovechando las ventajas del uso de puntos de conexión. Los puntos de conexión son una primitiva de bajo nivel como middleware y no están unidos a la implementación de enrutamiento. La mayoría de las aplicaciones no necesitan reemplazar `UseRouting` por lógica personalizada.

El middleware `UseEndpoints` está diseñado para usarse junto con el middleware `UseRouting`. La lógica básica para ejecutar un punto de conexión no es complicada. Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> para recuperar el punto de conexión y, después, invoque su propiedad <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.

En el código siguiente se muestra cómo el middleware puede influir en el enrutamiento o reaccionar ante este:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

En el ejemplo anterior se muestran dos conceptos importantes:

* El middleware se puede ejecutar antes de `UseRouting` para modificar los datos sobre los que funciona el enrutamiento.
    * Normalmente, el middleware que aparece antes del enrutamiento modifica alguna propiedad de la solicitud, como <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> o <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.
* El middleware se puede ejecutar entre `UseRouting` y <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> para procesar los resultados del enrutamiento antes de que se ejecute el punto de conexión.
    * El middleware que se ejecuta entre `UseRouting` y `UseEndpoints`:
      * Normalmente inspecciona los metadatos para entender los puntos de conexión.
      * A menudo toma decisiones de seguridad, como `UseAuthorization` y `UseCors`.
    * La combinación de middleware y metadatos permite configurar directivas por punto de conexión.

En el código anterior se muestra un ejemplo de middleware personalizado que admite directivas por punto de conexión. El middleware escribe un *registro de auditoría* de acceso a datos confidenciales en la consola. El middleware se puede configurar para *auditar* un punto de conexión con los metadatos de `AuditPolicyAttribute`. En este ejemplo se muestra un patrón *opcional* en el que solo se auditan los puntos de conexión marcados como confidenciales. Esta lógica se puede definir en orden inverso, para auditar todo lo que no esté marcado como seguro, por ejemplo. El sistema de metadatos de punto de conexión es flexible. Esta lógica se puede diseñar de la manera que mejor se adapte al caso de uso.

El código del ejemplo anterior está diseñado para mostrar los conceptos básicos de los puntos de conexión. **No está pensado para su uso en producción** . Una versión más completa de un middleware de *registro de auditoría* :

* Realizaría el registro en un archivo o una base de datos.
* Incluiría detalles como el usuario, la dirección IP, el nombre del punto de conexión confidencial, etc.

El valor `AuditPolicyAttribute` de metadatos de directiva de auditoría se define como `Attribute` para facilitar su uso con marcos basados en clases como los controladores y SignalR. Cuando se usa *de ruta a código* :

* Los metadatos se asocian con una API de generador.
* Los marcos basados en clases incluyen todos los atributos en el método y la clase correspondientes al crear los puntos de conexión.

Los procedimientos recomendados para los tipos de metadatos son definirlos como interfaces o atributos. Las interfaces y los atributos permiten la reutilización del código. El sistema de metadatos es flexible y no impone ninguna limitación.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Comparación entre un middleware de terminal y el enrutamiento

En el ejemplo de código siguiente se compara el uso de middleware con el del enrutamiento:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

El estilo de middleware que se muestra con `Approach 1:` es **middleware de terminal** . Se denomina middleware de terminal porque realiza una operación de búsqueda de coincidencias:

* La operación de búsqueda de coincidencias en el ejemplo anterior es `Path == "/"` para el middleware y `Path == "/Movie"` para el enrutamiento.
* Cuando una coincidencia es correcta, ejecuta alguna funcionalidad y devuelve un valor, en lugar de invocar el middleware `next`.

Se denomina middleware de terminal porque finaliza la búsqueda, ejecuta alguna funcionalidad y, después, devuelve un valor.

Comparación entre un middleware de terminal y el enrutamiento:
* Los dos enfoques permiten terminar la canalización de procesamiento:
    * El middleware finaliza la canalización mediante la devolución de un valor en lugar de invocar `next`.
    * Los puntos de conexión siempre son de terminal.
* El middleware de terminal permite colocar el middleware en un lugar arbitrario de la canalización:
    * Los puntos de conexión se ejecutan en la posición de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.
* El middleware de terminal permite que el código arbitrario determine cuándo coincide el middleware:
    * El código personalizado de búsqueda de coincidencia de rutas puede ser detallado y difícil de escribir correctamente.
    * El enrutamiento proporciona soluciones sencillas para las aplicaciones típicas. La mayoría de las aplicaciones no requieren código personalizado de búsqueda de coincidencia de rutas.
* Los puntos de conexión interactúan con middleware como `UseAuthorization` y `UseCors`.
    * Para usar un middleware de terminal con `UseAuthorization` o `UseCors` se necesita interactuar de forma manual con el sistema de autorización.

Un [punto de conexión](#endpoint) define:

* Un delegado para procesar solicitudes.
* Una colección de metadatos arbitrarios. Los metadatos se usan para implementar cuestiones transversales según las directivas y la configuración asociada a cada punto de conexión.

El middleware de terminal puede ser una herramienta eficaz, pero puede requerir:

* Una cantidad significativa de código y pruebas.
* La integración manual con otros sistemas para lograr el nivel deseado de flexibilidad.

Considere la posibilidad de realizar la integración con el enrutamiento antes de escribir middleware de terminal.

El middleware de terminal existente que se integra con [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) o <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> normalmente se puede convertir en un punto de conexión compatible con el enrutamiento. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) muestra el patrón para enrutadores:
* Escriba un método de extensión en <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.
* Cree una canalización de middleware anidada mediante <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.
* Adjunte el middleware a la nueva canalización. En este caso, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* Aplique <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> a la canalización de middleware en un objeto <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Llame a `Map` y proporcione la nueva canalización de middleware.
* Devuelva el objeto de generador proporcionado por `Map` desde el método de extensión.

En el código siguiente se muestra el uso de [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

En el ejemplo anterior se muestra la importancia de devolver el objeto de generador. Al devolver el objeto de generador, el desarrollador de aplicaciones puede configurar directivas como la autorización para el punto de conexión. En este ejemplo, el middleware de comprobaciones de estado no tiene una integración directa con el sistema de autorización.

El sistema de metadatos se ha creado como respuesta a los problemas detectados por los autores de extensibilidad mediante el middleware de terminal. El problema de cada middleware es implementar su propia integración con el sistema de autorización.

<a name="urlm"></a>

### <a name="url-matching"></a>Coincidencia de dirección URL

* Es el proceso por el cual el enrutamiento hace coincidir una solicitud entrante a un [punto de conexión](#endpoint).
* Se basa en los datos de la ruta de acceso y los encabezados de la dirección URL.
* Se puede extender para tener en cuenta los datos de la solicitud.

Cuando se ejecuta un middleware de enrutamiento, se establece un objeto `Endpoint` y se enrutan los valores a una [característica de solicitud](xref:fundamentals/request-features) en el objeto <xref:Microsoft.AspNetCore.Http.HttpContext> desde la solicitud actual:

* La llamada a [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) obtiene el punto de conexión.
* `HttpRequest.RouteValues` obtiene la colección de valores de ruta.

El [middleware](xref:fundamentals/middleware/index) que se ejecuta después del middleware de enrutamiento puede inspeccionar el punto de conexión y tomar medidas. Por ejemplo, un middleware de autorización puede consultar la colección de metadatos del punto de conexión de una directiva de autorización. Después de que se ejecuta todo el middleware en la canalización de procesamiento de solicitudes, se invoca al delegado del punto de conexión seleccionado.

El sistema de enrutamiento en el enrutamiento de punto de conexión es responsable de todas las decisiones relativas al envío. Como el middleware aplica directivas en función del punto de conexión seleccionado, es importante que:

* Cualquier decisión que pueda afectar al envío o a la aplicación de directivas de seguridad se realice dentro del sistema de enrutamiento.

> [!WARNING]
> En cuanto a la compatibilidad con versiones anteriores, cuando se ejecuta un delegado del punto de conexión de controlador o Razor Pages, las propiedades de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) se establecen en los valores adecuados en función del procesamiento de solicitudes realizado hasta el momento.
>
> El tipo `RouteContext` se marcará como obsoleto en una versión futura:
>
> * Migre `RouteData.Values` a `HttpRequest.RouteValues`.
> * Migre `RouteData.DataTokens` para recuperar [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) de los metadatos del punto de conexión.

La coincidencia de direcciones URL funciona en un conjunto configurable de fases. En cada fase, la salida es un conjunto de coincidencias. El conjunto de coincidencias se puede reducir más en la fase siguiente. La implementación de enrutamiento no garantiza un orden de procesamiento para los puntos de conexión coincidentes. **Todas** las coincidencias posibles se procesan a la vez. Las fases de coincidencia de direcciones URL se producen en el orden siguiente. ASP.NET Core:

1. Procesa la ruta de dirección URL con el conjunto de puntos de conexión y sus plantillas de ruta, y se recopilan **todas** las coincidencias.
1. Toma la lista anterior y quita las coincidencias en las que se produce un error con restricciones de ruta aplicadas.
1. Toma la lista anterior y quita las coincidencias en las que se produce un error en el conjunto de instancias de [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy).
1. Usa [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) para tomar una decisión final de la lista anterior.

La lista de puntos de conexión se prioriza según:

* El valor [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* La [precedencia de la plantilla de ruta](#rtp)

Todos los puntos de conexión coincidentes se procesan en cada fase hasta que se alcanza <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector>. `EndpointSelector` es la fase final. Elige el punto de conexión de prioridad más alta entre las coincidencias como la mejor coincidencia. Si hay otras coincidencias con la misma prioridad que la mejor, se inicia una excepción de coincidencia ambigua.

La prioridad de ruta se calcula en función de una plantilla de ruta **más específica** a la que se le asigna una prioridad más alta. Por ejemplo, considere las plantillas `/hello` y `/{message}`:

* Las dos coinciden con la ruta de dirección URL `/hello`.
* `/hello` es más específica y, por tanto, tiene mayor prioridad.

Por lo general, la precedencia de rutas realiza un buen trabajo de elegir la mejor coincidencia para los tipos de esquemas de dirección URL que se usan en la práctica. Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> solo cuando sea necesario para evitar una ambigüedad.

Debido a los tipos de extensibilidad que proporciona el enrutamiento, el sistema de enrutamiento no puede calcular las rutas ambiguas por adelantado. Considere un ejemplo como las plantillas de ruta `/{message:alpha}` y `/{message:int}`:

* La restricción `alpha` solo coincide con caracteres alfabéticos.
* La restricción `int` solo coincide con números.
* Estas plantillas tienen la misma prioridad de ruta, pero no hay ninguna dirección URL única con la que coincidan.
* Si el sistema de enrutamiento ha notificado un error de ambigüedad al iniciarse, bloquearía este caso de uso válido.

> [!WARNING]
>
> El orden de las operaciones dentro de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> no influye en el comportamiento del enrutamiento, con una excepción. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> y <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> asignan de forma automática un valor de orden a sus puntos de conexión en función del orden en el que se hayan invocado. Esto simula el comportamiento a largo plazo de los controladores sin que el sistema de enrutamiento proporcione las mismas garantías que las implementaciones de enrutamiento anteriores.
>
> En la implementación heredada de enrutamiento, es posible implementar la extensibilidad de enrutamiento que tiene una dependencia en el orden de procesamiento de las rutas. Enrutamiento de puntos de conexión en ASP.NET Core 3.0 y versiones posteriores:
> 
> * No tiene un concepto de rutas.
> * No proporciona garantías de ordenación. Todos los puntos de conexión se procesan a la vez.

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Prioridad de la plantilla de ruta y orden de selección de los puntos de conexión

La [prioridad de la plantilla de ruta](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) es un sistema que asigna a cada plantilla de ruta un valor en función de su especificidad. Precedencia de la plantilla de ruta:

* Evita la necesidad de ajustar el orden de los puntos de conexión en casos comunes.
* Intenta hacer coincidir las expectativas comunes del comportamiento del enrutamiento.

Por ejemplo, considere las plantillas `/Products/List` y `/Products/{id}`. Sería razonable suponer que `/Products/List` es una mejor coincidencia que `/Products/{id}` para la ruta de dirección URL `/Products/List`. Funciona porque el segmento literal `/List` se considera que tiene una mayor prioridad que el segmento de parámetro `/{id}`.

Los detalles de cómo funciona la precedencia están vinculados a cómo se definen las plantillas de ruta:

* Las plantillas con más segmentos se consideran más específicas.
* Un segmento con texto literal se considera más específico que un segmento de parámetro.
* Un segmento de parámetro con una restricción se considera más específico que uno que no la tenga.
* Un segmento complejo se considera igual de específico que un segmento de parámetro con una restricción.
* Los parámetros comodín son los menos específicos. Vea **comodín** en [Referencia de plantilla de ruta](#rtr) para obtener información importante sobre las rutas comodín.

Vea el [código fuente en GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) para obtener una referencia de los valores exactos.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Conceptos de generación de direcciones URL

Generación de direcciones URL:

* Es el proceso por el cual el enrutamiento puede crear una ruta de dirección URL en función de un conjunto de valores de ruta.
* Permite una separación lógica entre los puntos de conexión y las direcciones URL que acceden a ellos.

El enrutamiento de punto de conexión incluye la API <xref:Microsoft.AspNetCore.Routing.LinkGenerator>. `LinkGenerator` es un servicio singleton disponible desde la [DI](xref:fundamentals/dependency-injection). La API `LinkGenerator` se puede usar fuera del contexto de una solicitud en ejecución. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) y los escenarios que dependen de <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, como los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), los de HTML y los [resultados de acción](xref:mvc/controllers/actions), usan de forma interna la API `LinkGenerator` para proporcionar funciones de generación de vínculos.

El generador de vínculos está respaldado por el concepto de una **dirección** y **esquemas de direcciones** . Un esquema de direcciones es una manera de determinar los puntos de conexión que se deben tener en cuenta para la generación de vínculos. Por ejemplo, los escenarios de nombre y valores de ruta de controladores y Razor Pages con los que muchos usuarios están familiarizados se implementan como un esquema de direcciones.

El generador de vínculos puede vincular a controladores y Razor Pages a través de los métodos de extensión siguientes:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Las sobrecargas de estos métodos aceptan argumentos que incluyan `HttpContext`. Estos métodos son funcionalmente equivalentes a [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) y [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), pero ofrecen flexibilidad y opciones adicionales.

Los métodos `GetPath*` son más similares a `Url.Action` y `Url.Page`, dado que generan un URI que contiene una ruta de acceso absoluta. Los métodos `GetUri*` siempre generan un URI absoluto que contiene un esquema y un host. Los métodos que aceptan `HttpContext` generan un URI en el contexto de la solicitud que se ejecuta. A menos que se reemplacen, se usan los valores de ruta de [ambiente](#ambient), la ruta de acceso base de la dirección URL, el esquema y el host de la solicitud en ejecución.

Se llama a <xref:Microsoft.AspNetCore.Routing.LinkGenerator> con una dirección. La generación de un URI se produce en dos pasos:

1. Se enlaza una dirección a una lista de puntos de conexión que coincidan con la dirección.
1. Se evalúa el elemento <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> de cada punto de conexión hasta que se encuentra un patrón de ruta que coincida con los valores proporcionados. La salida resultante se combina con otras partes del URI proporcionadas al generador de vínculos y devueltas.

Los métodos proporcionados por <xref:Microsoft.AspNetCore.Routing.LinkGenerator> admiten funciones estándar de generación de vínculos para cualquier tipo de dirección. La forma más práctica de usar el generador de vínculos es a través de métodos de extensión que realicen operaciones para un tipo de dirección específica:

| Método de extensión | Descripción |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Genera un URI con una ruta de acceso absoluta en función de los valores proporcionados. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Genera un URI absoluto en función de los valores proporcionados.             |

> [!WARNING]
> Preste atención a las consecuencias siguientes de llamar a los métodos <xref:Microsoft.AspNetCore.Routing.LinkGenerator>:
>
> * Use los métodos de extensión `GetUri*` con precaución en una configuración de aplicación en la que no se valide el encabezado `Host` de las solicitudes entrantes. Si no se valida el encabezado `Host` de las solicitudes entrantes, la entrada de la solicitud que no sea de confianza se puede devolver al cliente en los URI de una página o vista. Se recomienda que todas las aplicaciones de producción configuren su servidor para validar el encabezado `Host` en función de valores válidos conocidos.
>
> * Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> con precaución en el middleware junto con `Map` o `MapWhen`. `Map*` cambia la ruta de acceso base de la solicitud que se ejecuta, lo que afecta a la salida de la generación de vínculos. Todas las API de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permiten especificar una ruta de acceso base. Especifique una ruta de acceso base vacía para deshacer el efecto de `Map*` en la generación de vínculos.

### <a name="middleware-example"></a>Ejemplo de middleware

En el ejemplo siguiente, un middleware usa la API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> para crear un vínculo a un método de acción que enumera los productos de la tienda. El uso del generador de vínculos mediante su inserción en una clase y la llamada a `GenerateLink` está disponible para cualquier clase de una aplicación:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Referencia de plantilla de ruta

Los tokens de `{}` definen parámetros de ruta que se enlazan si se encuentran coincidencias con la ruta. Se puede definir más de un parámetro de ruta en un segmento de ruta, pero deben estar separados por un valor literal. Por ejemplo, `{controller=Home}{action=Index}` no es una ruta válida, ya que no hay ningún valor literal entre `{controller}` y `{action}`.  Los parámetros de ruta deben tener un nombre y, opcionalmente, atributos adicionales especificados.

El texto literal diferente de los parámetros de ruta (por ejemplo, `{id}`) y el separador de ruta `/` deben coincidir con el texto de la dirección URL. La coincidencia de texto no distingue mayúsculas de minúsculas y se basa en la representación descodificada de la ruta de las direcciones URL. Para que el delimitador de parámetro de ruta literal `{` o `}` coincida, repita el carácter para aplicar escape al carácter. Por ejemplo, `{{` o `}}`.

Asterisco `*` o asterisco doble `**`:

* Se puede usar como prefijo de un parámetro de ruta para enlazar con el resto del URI.
* Se denominan parámetros **comodín** . Por ejemplo, `blog/{**slug}`:
  * Coincide con cualquier URI que empiece por `/blog` y después tenga cualquier valor.
  * El valor que aparece detrás de `/blog` se asigna al valor de ruta [slug](https://developer.mozilla.org/docs/Glossary/Slug).

[!INCLUDE[](~/includes/catchall.md)]

Los parámetros comodín también pueden coincidir con una cadena vacía.

El parámetro comodín inserta los caracteres de escape correspondientes cuando se usa la ruta para generar una dirección URL, incluidos los caracteres `/` de separación de ruta de acceso. Por ejemplo, la ruta `foo/{*path}` con valores de ruta `{ path = "my/path" }` genera `foo/my%2Fpath`. Tenga en cuenta la barra diagonal de escape. Para los caracteres separadores de ruta de acceso de ida y vuelta, use el prefijo de parámetro de ruta `**`. La ruta `foo/{**path}` con `{ path = "my/path" }` genera `foo/my/path`.

Los patrones de dirección URL que intentan capturar un nombre de archivo con una extensión de archivo opcional tienen consideraciones adicionales. Por ejemplo, considere la plantilla `files/{filename}.{ext?}`. Cuando existen valores para `filename` y `ext`, los dos valores se rellenan. Si solo existe un valor para `filename` en la dirección URL, la ruta coincide porque el carácter `.` final es opcional. Las direcciones URL siguientes coinciden con esta ruta:

* `/files/myFile.txt`
* `/files/myFile`

Los parámetros de ruta pueden tener **valores predeterminados** designados mediante la especificación del valor predeterminado después del nombre de parámetro, separado por un signo igual (`=`). Por ejemplo, `{controller=Home}` define `Home` como el valor predeterminado de `controller`. El valor predeterminado se usa si no hay ningún valor en la dirección URL para el parámetro. Los parámetros de ruta se pueden convertir en opcionales si se anexa un signo de interrogación (`?`) al final del nombre del parámetro. Por ejemplo: `id?`. La diferencia entre los valores opcionales y los parámetros de ruta predeterminados es:

* Un parámetro de ruta con un valor predeterminado siempre produce un valor.
* Un parámetro opcional solo tiene un valor cuando la dirección URL de la solicitud proporciona un valor.

Los parámetros de ruta pueden tener restricciones que deben coincidir con el valor de ruta enlazado desde la dirección URL. Al agregar `:` y un nombre de restricción después del nombre del parámetro de ruta, se especifica una restricción insertada en un parámetro de ruta. Si la restricción requiere argumentos, se incluyen entre paréntesis `(...)` después del nombre de restricción. Se pueden especificar varias *restricciones insertadas* si se anexa otro carácter `:` y un nombre de restricción.

El nombre de restricción y los argumentos se pasan al servicio <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para crear una instancia de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> para su uso en el procesamiento de direcciones URL. Por ejemplo, la plantilla de ruta `blog/{article:minlength(10)}` especifica una restricción `minlength` con el argumento `10`. Para obtener más información sobre las restricciones de ruta y una lista de las restricciones proporcionadas por el marco de trabajo, vea la sección [Referencia de restricciones de ruta](#route-constraint-reference).

Los parámetros de ruta también pueden tener transformadores de parámetros. Los transformadores de parámetros transforman el valor de un parámetro al generar vínculos y hacer coincidir acciones y páginas con direcciones URL. Como sucede con las restricciones, los transformadores de parámetros se pueden agregar en línea a un parámetro de ruta mediante la incorporación un carácter `:` y un nombre de transformador después del nombre del parámetro de ruta. Por ejemplo, la plantilla de ruta `blog/{article:slugify}` especifica un transformador `slugify`. Para obtener más información sobre los transformadores de parámetros, vea la sección [Referencia de transformadores de parámetros](#parameter-transformer-reference).

En la tabla siguiente se muestran plantillas de ruta de ejemplo y su comportamiento:

| Plantilla de ruta                           | URI coincidente de ejemplo    | El URI de la solicitud&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Solo coincide con la ruta de acceso única `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Coincide y establece `Page` en `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Coincide y establece `Page` en `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Se asigna al controlador `Products` y la acción `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Se asigna al controlador `Products` y la acción `Details` con `id` establecido en 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Se asigna al controlador `Home` y al método `Index`. `id` se pasa por alto.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Se asigna al controlador `Products` y al método `Index`. `id` se pasa por alto.        |

El uso de una plantilla suele ser el método de enrutamiento más sencillo. Las restricciones y los valores predeterminados también se pueden especificar fuera de la plantilla de ruta.

### <a name="complex-segments"></a>Segmentos complejos

Los segmentos complejos se procesan mediante la búsqueda de coincidencias de delimitadores literales de derecha a izquierda de un modo [no expansivo](#greedy). Por ejemplo, `[Route("/a{b}c{d}")]` es un segmento complejo.
Los segmentos complejos funcionan de una manera determinada que se debe entender para usarlos correctamente. En el ejemplo de esta sección se muestra por qué los segmentos complejos solo funcionan bien cuando el texto del delimitador no aparece dentro de los valores de los parámetros. En casos más complejos es necesario usar una [expresión regular](/dotnet/standard/base-types/regular-expressions) y extraer los valores de forma manual.

[!INCLUDE[](~/includes/regex.md)]

Este es un resumen de los pasos que realiza el enrutamiento con la plantilla `/a{b}c{d}` y la ruta de dirección URL `/abcd`. `|` se usa para ayudar a visualizar cómo funciona el algoritmo:

* El primer literal, de derecha a izquierda, es `c`. Por tanto, se busca en `/abcd` desde la derecha y se encuentra `/ab|c|d`.
* Todo lo que se encuentra a la derecha (`d`) coincide ahora con el parámetro de ruta `{d}`.
* El siguiente literal, de derecha a izquierda, es `a`. Por tanto, se busca en `/ab|c|d` a partir de donde se ha parado antes, después `a` y se encuentra `/|a|b|c|d`.
* El valor situado a la derecha (`b`) coincide ahora con el parámetro de ruta `{b}`.
* No queda ningún texto ni ninguna plantilla de ruta, por lo que se trata de una coincidencia.

Este es un ejemplo de un caso negativo en el que se usa la misma plantilla `/a{b}c{d}` y la ruta de dirección URL `/aabcd`. `|` se usa para ayudar a visualizar cómo funciona el algoritmo. Este caso no es una coincidencia, que se explica mediante el mismo algoritmo:
* El primer literal, de derecha a izquierda, es `c`. Por tanto, se busca en `/aabcd` desde la derecha y se encuentra `/aab|c|d`.
* Todo lo que se encuentra a la derecha (`d`) coincide ahora con el parámetro de ruta `{d}`.
* El siguiente literal, de derecha a izquierda, es `a`. Por tanto, se busca en `/aab|c|d` a partir de donde se ha parado antes, después `a` y se encuentra `/a|a|b|c|d`.
* El valor situado a la derecha (`b`) coincide ahora con el parámetro de ruta `{b}`.
* En este momento, todavía hay texto `a`, pero el algoritmo se ha quedado sin plantilla de ruta para analizar, por lo que no es una coincidencia.

Como el algoritmo de búsqueda de coincidencias es [no expansivo](#greedy):

* Coincide con la menor cantidad de texto posible en cada paso.
* Cualquier caso en el que el valor de delimitador aparezca dentro de los valores de parámetro provoca que no coincida.

Las expresiones regulares proporcionan un mayor control sobre el comportamiento de búsqueda de coincidencias.

<a name="greedy"></a>

La coincidencia expansiva, también conocida como [coincidencia diferida](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), coincide con la cadena más grande posible. La búsqueda no expansiva coincide con la cadena más pequeña posible.

## <a name="route-constraint-reference"></a>Referencia de restricción de ruta

Las restricciones de ruta se ejecutan cuando se ha producido una coincidencia con la dirección URL entrante y la ruta de dirección URL se convierte en tokens en valores de ruta. En general, las restricciones de ruta inspeccionan el valor de ruta asociado a través de la plantilla de ruta y deciden si el valor es aceptable o no. Algunas restricciones de ruta usan datos ajenos al valor de ruta para decidir si la solicitud se puede enrutar. Por ejemplo, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> puede aceptar o rechazar una solicitud basada en su verbo HTTP. Las restricciones se usan en las solicitudes de enrutamiento y la generación de vínculos.

> [!WARNING]
> No use las restricciones para la validación de entradas. Si se usan restricciones para la validación de entradas, las que no sean válidas generan una respuesta `404` No encontrado. Una entrada no válida debería generar `400` Solicitud incorrecta con un mensaje de error adecuado. Las restricciones de ruta se usan para eliminar la ambigüedad entre rutas similares, no para validar las entradas de una ruta determinada.

En la tabla siguiente se muestran restricciones de ruta de ejemplo y su comportamiento esperado:

| restricción | Ejemplo | Coincidencias de ejemplo | Notas |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Coincide con cualquier entero |
| `bool` | `{active:bool}` | `true`, `FALSE` | Coincide con `true` o `false`. No distingue mayúsculas de minúsculas |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Coincide con un valor `DateTime` válido en la referencia cultural invariable. Vea la advertencia anterior. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Coincide con un valor `decimal` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Coincide con un valor `double` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Coincide con un valor `float` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Coincide con un valor `Guid` válido |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Coincide con un valor `long` válido |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La cadena debe tener al menos cuatro caracteres |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La cadena no debe tener más de ocho caracteres |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La cadena debe tener una longitud de exactamente 12 caracteres |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La cadena debe tener una longitud como mínimo de ocho caracteres y como máximo de 16 |
| `min(value)` | `{age:min(18)}` | `19` | El valor entero debe ser como mínimo 18 |
| `max(value)` | `{age:max(120)}` | `91` | El valor entero debe ser como máximo 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | El valor entero debe ser como mínimo 18 y máximo 120 |
| `alpha` | `{name:alpha}` | `Rick` | La cadena debe constar de uno o más caracteres alfabéticos, `a`-`z` y no distinguir mayúsculas de minúsculas. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La cadena debe coincidir con la expresión regular. Vea las sugerencias sobre cómo definir una expresión regular. |
| `required` | `{name:required}` | `Rick` | Se usa para exigir que un valor que no es de parámetro esté presente durante la generación de dirección URL |

[!INCLUDE[](~/includes/regex.md)]

Es posible aplicar varias restricciones delimitadas por dos puntos a un único parámetro. Por ejemplo, la siguiente restricción permite limitar un parámetro a un valor entero de 1 o superior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR siempre usan la referencia cultural invariable. Por ejemplo, la conversión al tipo `int` o `DateTime` de CLR. Estas restricciones dan por supuesto que la dirección URL no es localizable. Las restricciones de ruta proporcionadas por el marco de trabajo no modifican los valores almacenados en los valores de ruta. Todos los valores de ruta analizados desde la dirección URL se almacenan como cadenas. Por ejemplo, la restricción `float` intenta convertir el valor de ruta en un valor Float, pero el valor convertido se usa exclusivamente para comprobar que se puede convertir en Float.

### <a name="regular-expressions-in-constraints"></a>Expresiones regulares en restricciones

[!INCLUDE[](~/includes/regex.md)]

Las expresiones regulares se pueden especificar como restricciones insertadas mediante la restricción de ruta `regex(...)`. Los métodos de la familia <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> también aceptan un literal de objeto de restricciones. Si se usa ese formato, los valores de cadena se interpretan como expresiones regulares.

En el código siguiente se usa una restricción de expresión regular insertada:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

En el código siguiente se usa un literal de objeto para especificar una restricción de expresión regular:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

El marco de trabajo de ASP.NET Core agrega `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` al constructor de expresiones regulares. Vea <xref:System.Text.RegularExpressions.RegexOptions> para obtener una descripción de estos miembros.

Las expresiones regulares usan delimitadores y tokens similares a los que usan el enrutamiento y el lenguaje C#. Es necesario usar secuencias de escape con los tokens de expresiones regulares. Para usar la expresión regular `^\d{3}-\d{2}-\d{4}$` en una restricción insertada, utilice una de las opciones siguientes:

* Reemplace los caracteres `\` proporcionados en la cadena como caracteres `\\` en el archivo de código fuente de C# para aplicar secuencias de escape al carácter de escape de cadena `\`.
* [Literales de cadena textual](/dotnet/csharp/language-reference/keywords/string).

Para aplicar secuencias de escape a los caracteres delimitadores de parámetro de enrutamiento (`{`, `}`, `[` y `]`), duplique los caracteres en la expresión, por ejemplo `{{`, `}}`, `[[` y `]]`. En la tabla siguiente se muestra una expresión regular y su versión con la secuencia de escape:

| Expresión regular    | Expresión regular con secuencia de escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Las expresiones regulares que se usan en el enrutamiento suelen empezar con el carácter `^` y coincidir con la posición inicial de la cadena. Las expresiones suelen terminar con el carácter `$` y coincidir con el final de la cadena. Los caracteres `^` y `$` garantizan que la expresión regular coincide con el valor completo del parámetro de ruta. Sin los caracteres `^` y `$`, la expresión regular coincide con cualquier subcadena de la cadena, lo que normalmente no es deseable. En la tabla siguiente se proporcionan ejemplos y se explica por qué coinciden o no:

| Expresión   | String    | Coincidir con | Comentario               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | 123abc456 | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | mz        | Sí   | Coincide con la expresión    |
| `[a-z]{2}`   | MZ        | Sí   | No distingue mayúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | No    | Vea `^` y `$` más arriba |
| `^[a-z]{2}$` | 123abc456 | No    | Vea `^` y `$` más arriba |

Para obtener más información sobre la sintaxis de expresiones regulares, vea [Expresiones regulares de .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir un parámetro a un conjunto conocido de valores posibles, use una expresión regular. Por ejemplo, `{action:regex(^(list|get|create)$)}` solo hace coincidir el valor de ruta `action` con `list`, `get` o `create`. Si se pasa al diccionario de restricciones, la cadena `^(list|get|create)$` es equivalente. Las restricciones que se pasan al diccionario de restricciones que no coinciden con una de las conocidas también se tratan como expresiones regulares. Las restricciones que se pasan en una plantilla y que no coinciden con una de las conocidas no se tratan como expresiones regulares.

### <a name="custom-route-constraints"></a>Restricciones de ruta personalizadas

Se pueden crear restricciones de ruta personalizadas mediante la implementación de la interfaz <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. La interfaz `IRouteConstraint` contiene <xref:System.Web.Routing.IRouteConstraint.Match*>, que devuelve `true` si se cumple la restricción, y `false` en caso contrario.

Las restricciones de ruta personalizadas rara vez son necesarias. Antes de implementar una restricción de ruta personalizada, considere alternativas, como el enlace de modelos.

En la carpeta [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) de ASP.NET Core se proporcionan buenos ejemplos de creación de restricciones. Por ejemplo, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).

Para usar una restricción `IRouteConstraint` personalizada, el tipo de restricción de ruta se debe registrar con el parámetro <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de la aplicación en el contenedor de servicios. `ConstraintMap` es un diccionario que asigna claves de restricciones de ruta a implementaciones de `IRouteConstraint` que validen esas restricciones. El parámetro `ConstraintMap` de una aplicación puede actualizarse en `Startup.ConfigureServices` como parte de una llamada a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) o configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> directamente con `services.Configure<RouteOptions>`. Por ejemplo:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

La restricción anterior se aplica en el código siguiente:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

La implementación de `MyCustomConstraint` impide que `0` se aplique a un parámetro de ruta:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

El código anterior:

* Impide `0` en el segmento `{id}` de la ruta.
* Se muestra para proporcionar un ejemplo básico de implementación de una restricción personalizada. No se debe usar en una aplicación de producción.

El código siguiente es un enfoque mejor para impedir que se procese un valor `id` que contenga `0`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

El código anterior tiene las ventajas siguientes con respecto al enfoque de `MyCustomConstraint`:

* No requiere una restricción personalizada.
* Devuelve un error más descriptivo cuando el parámetro de ruta incluye `0`.

## <a name="parameter-transformer-reference"></a>Referencia de transformadores de parámetros

Transformadores de parámetros:

* Se ejecutan al generar un vínculo mediante <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.
* Implemente <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Se configuran con <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Toman el valor de ruta del parámetro y lo transforman en un nuevo valor de cadena.
* Como resultado, el valor transformado se usa en el vínculo generado.

Por ejemplo, un transformador de parámetros personalizado `slugify` en el patrón de ruta `blog\{article:slugify}` con `Url.Action(new { article = "MyTestArticle" })` genera `blog\my-test-article`.

Considere la siguiente implementación de `IOutboundParameterTransformer`:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Para usar un transformador de parámetros en un patrón de ruta, configúrelo con <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> en `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

El marco ASP.NET Core usa los transformadores de parámetros para transformar el URI en el que se resuelve un punto de conexión. Por ejemplo, los transformadores de parámetros transforman los valores de ruta que se usan para hacer coincidir objetos `area`, `controller`, `action` y `page`.

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Con la plantilla de ruta anterior, la acción `SubscriptionManagementController.GetAll` coincide con el URI `/subscription-management/get-all`. Un transformador de parámetros no cambia los valores de ruta usados para generar un vínculo. Por ejemplo, `Url.Action("GetAll", "SubscriptionManagement")` genera `/subscription-management/get-all`.

ASP.NET Core proporciona convenciones de API para usar transformadores de parámetros con rutas generadas:

* La convención <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> de MVC aplica un transformador de parámetro especificado a todas las rutas de atributo de la aplicación. El transformador de parámetro transforma los tokens de rutas de atributo a medida que se reemplazan. Para más información, consulte [Usar un transformador de parámetro para personalizar el reemplazo de tokens](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Pages usa la convención de API Razor. Esta convención aplica un transformador de parámetros especificado a todas las instancias de Razor Pages detectadas de forma automática. El transformador de parámetros transforma los segmentos de nombre de archivo y carpeta de las rutas de Razor Pages. Para más información, consulte el artículo sobre cómo [usar un transformador de parámetro para personalizar rutas de Razor Pages](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Referencia de generación de direcciones URL

Esta sección contiene una referencia para el algoritmo implementado por la generación de direcciones URL. En la práctica, los ejemplos más complejos de generación de direcciones URL usan controladores o Razor Pages. Vea [Enrutamiento en controladores](xref:mvc/controllers/routing) para obtener información adicional.

El proceso de generación de direcciones URL comienza con una llamada a [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) o un método similar. Al método se le proporciona una dirección, un conjunto de valores de ruta y, opcionalmente, información sobre la solicitud actual de `HttpContext`.

El primer paso consiste en usar la dirección para resolver un conjunto de puntos de conexión candidatos con una instancia de [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) que coincide con el tipo de la dirección.

Una vez que el esquema de direcciones encuentra un conjunto de candidatos, los puntos de conexión se ordenan y procesan de forma iterativa hasta que una operación de generación de direcciones URL se realiza correctamente. La generación de direcciones URL **no** comprueba si hay ambigüedades; el primer resultado devuelto es el resultado final.

### <a name="troubleshooting-url-generation-with-logging"></a>Solución de problemas de generación de direcciones URL con registro

El primer paso para solucionar problemas de generación de direcciones URL consiste en establecer el nivel de registro de `Microsoft.AspNetCore.Routing` en `TRACE`. `LinkGenerator` registra muchos detalles sobre su procesamiento que pueden ser útiles para solucionar problemas.

Vea [Referencia de generación de direcciones URL](#ugr) para obtener más información sobre la generación de direcciones URL.

### <a name="addresses"></a>Direcciones

Las direcciones son el concepto de la generación de direcciones URL que se usa para enlazar una llamada al generador de vínculos a un conjunto de puntos de conexión candidatos.

Las direcciones son un concepto extensible que incluyen dos implementaciones de forma predeterminada:

* Con el *nombre del punto de conexión* (`string`) como dirección:
    * Proporciona una funcionalidad similar al nombre de ruta de MVC.
    * Usa el tipo de metadatos de <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata>.
    * Resuelve la cadena proporcionada con los metadatos de todos los puntos de conexión registrados.
    * Inicia una excepción durante el inicio si varios puntos de conexión usan el mismo nombre.
    * Se recomienda para uso general fuera de los controladores y Razor Pages.
* Con los *valores de ruta* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) como dirección:
    * Proporciona una funcionalidad similar a los controladores y la generación de direcciones URL heredada de Razor Pages.
    * La ampliación y depuración son complejas.
    * Proporciona la implementación que usa `IUrlHelper`, aplicaciones auxiliares de etiquetas, aplicaciones auxiliares HTML, resultados de acciones, etc.

El papel del esquema de direcciones consiste en establecer la asociación entre la dirección y los puntos de conexión coincidentes mediante criterios arbitrarios:

* El esquema de nombres de punto de conexión realiza una búsqueda de diccionario básica.
* El esquema de valores de ruta tiene un subconjunto óptimo de algoritmos definidos complejo.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Valores de ambiente y valores explícitos

A partir de la solicitud actual, el enrutamiento accede a los valores de ruta del objeto `HttpContext.Request.RouteValues` de la solicitud actual. Los valores asociados a la solicitud actual se conocen como **valores de ambiente** . Para mayor claridad, en la documentación se hace referencia a los valores de ruta que se pasan a los métodos como **valores explícitos** .

En el ejemplo siguiente se muestran valores de ambiente y valores explícitos. Proporciona valores de ambiente de la solicitud actual y valores explícitos, `{ id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

El código anterior:

* Devuelve `/Widget/Index/17`
* Obtiene <xref:Microsoft.AspNetCore.Routing.LinkGenerator> a través de la [DI](xref:fundamentals/dependency-injection).

En el código siguiente no se proporcionan valores de ambiente y valores explícitos, `{ controller = "Home", action = "Subscribe", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

El método anterior devuelve `/Home/Subscribe/17`

El código siguiente en `WidgetController` devuelve `/Widget/Subscribe/17`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

En el código siguiente se proporciona el controlador a partir de los valores de ambiente de la solicitud actual y los valores explícitos, `{ action = "Edit", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

En el código anterior:

* Se devuelve `/Gadget/Edit/17`.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> obtiene el objeto <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
genera una dirección URL con una ruta de acceso absoluta para un método de acción. La dirección URL contiene el nombre de `action` especificado y los valores `route`.

En el código siguiente se proporcionan valores de ambiente de la solicitud actual y valores explícitos: `{ page = "./Edit, id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

En el código anterior se establece `url` en `/Edit/17` cuando la página de Razor de edición contiene la siguiente directiva de página:

 `@page "{id:int}"`

Si la página de edición no contiene la plantilla de ruta `"{id:int}"`, `url` es `/Edit?id=17`.

El comportamiento de <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> de MVC agrega una capa de complejidad además de las reglas descritas aquí:

* `IUrlHelper` siempre proporciona los valores de ruta de la solicitud actual como valores de ambiente.
* [IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) siempre copia los valores de ruta `action` y `controller` actuales como valores explícitos a menos que el desarrollador los invalide.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) siempre copia el valor de ruta `page` actual como un valor explícito a menos que se invalide. <!--by the user-->
* `IUrlHelper.Page` siempre invalida el valor de ruta `handler` actual con `null` como un valor explícito a menos que se invalide.

A los usuarios a menudo les sorprenden los detalles del comportamiento de los valores de ambiente, ya que MVC no parece seguir sus propias reglas. Por motivos históricos y de compatibilidad, algunos valores de ruta como `action`, `controller`, `page` y `handler` tienen su propio comportamiento de caso especial.

La funcionalidad equivalente proporcionada por `LinkGenerator.GetPathByAction` y `LinkGenerator.GetPathByPage` duplica estas anomalías de `IUrlHelper` por motivos de compatibilidad.

### <a name="url-generation-process"></a>Proceso de generación de direcciones URL

Una vez que se encuentra el conjunto de puntos de conexión candidatos, el algoritmo de generación de direcciones URL:

* Procesa los puntos de conexión de forma iterativa.
* Devuelve el primer resultado correcto.

El primer paso de este proceso se denomina **invalidación del valor de ruta** .  La invalidación del valor de ruta es el proceso por el que el enrutamiento decide qué valores de ruta de los valores de ambiente se deben usar y cuáles se deben omitir. Cada valor de ambiente se tiene en cuenta y se combina con los valores explícitos, o bien se pasa por alto.

La mejor manera de pensar en el rol de los valores de ambiente es que intentan ahorrar trabajo a los desarrolladores de aplicaciones, en algunos casos comunes. Tradicionalmente, los escenarios en los que los valores de ambiente son útiles están relacionados con MVC:

* Al vincular a otra acción en el mismo controlador, no es necesario especificar el nombre del controlador.
* Al vincular a otro controlador en la misma área, no es necesario especificar el nombre del área.
* Al vincular al mismo método de acción, no es necesario especificar los valores de ruta.
* Al vincular a otro elemento de la aplicación, no le interesa transferir valores de ruta que no tengan ningún significado en ese elemento del control de la aplicación.

Las llamadas a `LinkGenerator` o `IUrlHelper` que devuelven `null` se suelen deber a que no se comprende la invalidación del valor de ruta. Para solucionar problemas de invalidación del valor de ruta, especifique de forma explícita más valores de ruta para ver si eso resuelve el problema.

La invalidación del valor de ruta se basa en la suposición de que el esquema de direcciones URL de la aplicación es jerárquico, con una jerarquía formada de izquierda a derecha. Considere la posibilidad de usar la plantilla de ruta de controlador básica `{controller}/{action}/{id?}` para hacerse una idea intuitiva de cómo funciona esto en la práctica. Un **cambio** en un valor **invalida** todos los valores de ruta que aparecen a la derecha. Esto refleja la suposición sobre la jerarquía. Si la aplicación tiene un valor de ambiente para `id` y la operación especifica otro valor para `controller`:

* `id` no se reutilizará porque `{controller}` está a la izquierda de `{id?}`.

Algunos ejemplos demuestran este principio:

* Si los valores explícitos contienen un valor para `id`, se omite el valor de ambiente de `id`. Se pueden usar los valores de ambiente para `controller` y `action`.
* Si los valores explícitos contienen un valor para `action`, se omite cualquier valor de ambiente para `action`. Se pueden usar los valores de ambiente para `controller`. Si el valor explícito para `action` es diferente del valor de ambiente para `action`, no se usará el valor de `id`.  Si el valor explícito para `action` es diferente del valor de ambiente para `action`, se puede usar el valor de `id`.
* Si los valores explícitos contienen un valor para `controller`, se omite cualquier valor de ambiente para `controller`. Si el valor explícito para `controller` es diferente del valor de ambiente para `controller`, no se usarán los valores de `action` y `id`. Si el valor explícito para `controller` es igual que el valor de ambiente para `controller`, se pueden usar los valores de `action` y `id`.

Este proceso sea complica todavía más por la existencia de rutas de atributo y rutas convencionales dedicadas. Las rutas convencionales de controlador, como `{controller}/{action}/{id?}`, especifican una jerarquía mediante parámetros de ruta. Para las [rutas convencionales dedicadas](xref:mvc/controllers/routing#dcr) y las [rutas de atributo](xref:mvc/controllers/routing#ar) a controladores y Razor Pages:

* Existe una jerarquía de valores de ruta.
* No aparecen en la plantilla.

En estos casos, la generación de direcciones URL define el concepto de **valores necesarios** . Los puntos de conexión creados por controladores y Razor Pages tienen valores necesarios especificados que permiten que la invalidación del valor de ruta funcione.

El algoritmo de invalidación del valor de ruta en detalle:

* Los nombres de valor necesarios se combinan con los parámetros de ruta y, después, se procesan de izquierda a derecha.
* Para cada parámetro, se comparan el valor de ambiente y el valor explícito:
    * Si el valor de ambiente y el valor explícito son iguales, el proceso continúa.
    * Si el valor de ambiente está presente y el valor explícito no, se usa el valor de ambiente al generar la dirección URL.
    * Si el valor de ambiente no está presente y el valor explícito sí, rechace el valor de ambiente y todos los posteriores.
    * Si el valor de ambiente y el valor explícito están presentes, y los dos son diferentes, rechace el valor de ambiente y todos los posteriores.

En este punto, la operación de generación de direcciones URL está lista para evaluar las restricciones de ruta. El conjunto de valores aceptados se combina con los valores predeterminados de parámetro, que se proporcionan a las restricciones. Si todas las restricciones son correctas, la operación continúa.

A continuación, se pueden usar los **valores aceptados** para expandir la plantilla de ruta. La plantilla de ruta se procesa:

* De izquierda a derecha.
* En cada parámetro se sustituye su valor aceptado.
* Con los siguientes casos especiales:
  * Si falta un valor en los valores aceptados y el parámetro tiene un valor predeterminado, se usa el valor predeterminado.
  * Si falta un valor en los valores aceptados y el parámetro es opcional, el procesamiento continúa.
  * Si un parámetro de ruta a la derecha de un parámetro opcional que falta tiene un valor, se produce un error en la operación.
  * <!-- review default-valued parameters optional parameters --> Los parámetros con valores predeterminados contiguos y los parámetros opcionales se contraen siempre que sea posible.

Los valores que se proporcionan de forma explícita que no coinciden con un segmento de la ruta se agregan a la cadena de consulta. En la tabla siguiente se muestra el resultado cuando se usa la plantilla de ruta `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Resultado                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problemas con la invalidación del valor de ruta

A partir de ASP.NET Core 3.0, algunos esquemas de generación de direcciones URL que se usaban en versiones anteriores de ASP.NET Core no funcionan bien con la generación de direcciones URL. El equipo de ASP.NET Core planea agregar características para abordar estas necesidades en una versión futura. Por ahora, la mejor solución consiste en usar el enrutamiento heredado.

En el código siguiente se muestra un ejemplo de un esquema de generación de direcciones URL que no es compatible con el enrutamiento.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

En el código anterior, se usa el parámetro de ruta `culture` para la localización. El objetivo es que el parámetro `culture` siempre se acepte como valor de ambiente. Pero el parámetro `culture` no se acepta como valor de ambiente debido al funcionamiento de los valores necesarios:

* En la plantilla de ruta `"default"`, el parámetro de ruta `culture` está a la izquierda de `controller`, por lo que los cambios en `controller` no invalidarán `culture`.
* En la plantilla de ruta `"blog"`, se considera que el parámetro de ruta `culture` está a la derecha de `controller`, que aparece en los valores necesarios.

## <a name="configuring-endpoint-metadata"></a>Configuración de metadatos de punto de conexión

Los vínculos siguientes proporcionan información sobre la configuración de metadatos de punto de conexión:

* [Habilitación de CORS con enrutamiento de punto de conexión](xref:security/cors#enable-cors-with-endpoint-routing)
* [Ejemplo de IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) con un atributo `[MinimumAgeAuthorize]` personalizado
* [Autenticación de pruebas con el atributo [Authorize]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Selección del esquema con el atributo [Authorize]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Aplicación de directivas con el atributo [Authorize]](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Comparación de host en rutas con RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> aplica una restricción a la ruta que requiere el host especificado. El parámetro `RequireHost` o [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) puede ser:

* Host: `www.domain.com`, compara `www.domain.com` con cualquier puerto.
* Host con carácter comodín: `*.domain.com`, coincide con `www.domain.com`, `subdomain.domain.com` o `www.subdomain.domain.com` en cualquier puerto.
* Puerto: `*:5000`, coincide con el puerto 5000 con cualquier host.
* Host y puerto: `www.domain.com:5000` o `*.domain.com:5000`, coincide con el host y el puerto.

Se pueden especificar varios parámetros mediante `RequireHost` o `[Host]`. La restricción coincide con los hosts válidos para cualquiera de los parámetros. Por ejemplo, `[Host("domain.com", "*.domain.com")]` coincide con `domain.com`, `www.domain.com` y `subdomain.domain.com`.

En el código siguiente se usa `RequireHost` para requerir el host especificado en la ruta:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

En el código siguiente se usa el atributo `[Host]` en el controlador para requerir cualquiera de los hosts especificados:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Cuando el atributo `[Host]` se aplica al método de acción y al controlador:

* Se usa el atributo en la acción.
* Se omite el atributo del controlador.

## <a name="performance-guidance-for-routing"></a>Instrucciones de rendimiento para el enrutamiento

La mayor parte del enrutamiento se ha actualizado en ASP.NET Core 3.0 para aumentar el rendimiento.

Cuando una aplicación tiene problemas de rendimiento, a menudo se sospecha que el enrutamiento es el problema. El motivo es que marcos como los controladores y Razor Pages notifican la cantidad de tiempo empleado en el marco de trabajo en sus mensajes de registro. Cuando hay una diferencia significativa entre el tiempo notificado por los controladores y el tiempo total de la solicitud:

* Los desarrolladores eliminan el código de la aplicación como origen del problema.
* Es habitual asumir que el enrutamiento es la causa.

El rendimiento del enrutamiento se prueba mediante miles de puntos de conexión. No es probable que una aplicación típica detecte un problema de rendimiento simplemente por ser demasiado grande. La causa raíz más común del rendimiento lento del enrutamiento suele ser middleware personalizado con un comportamiento incorrecto.

En el ejemplo de código siguiente se muestra una técnica básica para limitar el origen del retraso:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Para controlar el tiempo del enrutamiento:

* Intercale cada middleware con una copia del middleware de tiempo que se muestra en el código anterior.
* Agregue un identificador único para poner en correlación los datos de control de tiempo con el código.

Se trata de una forma básica de reducir el retraso cuando es significativo, por ejemplo, de más de `10ms`.  Al restar `Time 2` de `Time 1` se notifica el tiempo invertido dentro del middleware `UseRouting`.

En el código siguiente se usa un enfoque más compacto del código de control de tiempo anterior:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Características de enrutamiento potencialmente costosas

En la lista siguiente se proporciona información sobre las características de enrutamiento que son relativamente costosas en comparación con las plantillas de ruta básicas:

* Expresiones regulares: Se pueden escribir expresiones regulares que sean complejas o que tengan un tiempo de ejecución de larga duración con una pequeña cantidad de entradas.

* Segmentos complejos (`{x}-{y}-{z}`): 
  * Son mucho más costosos que analizar un segmento de ruta de dirección URL convencional.
  * El resultado es que se asignan muchas más subcadenas.
  * La lógica de segmentos complejos no se ha actualizado en la actualización de rendimiento de enrutamiento de ASP.NET Core 3.0.

* Acceso a datos sincrónicos: muchas aplicaciones complejas tienen acceso a bases de datos como parte de su enrutamiento. Es posible que en ASP.NET Core 2.2 y versiones anteriores el enrutamiento no proporcionara los puntos de extensibilidad correctos para admitir el enrutamiento de acceso a bases de datos. Por ejemplo, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> y <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> son sincrónicos. Los puntos de extensibilidad como <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> y <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> son asincrónicos.

## <a name="guidance-for-library-authors"></a>Instrucciones para los autores de bibliotecas

Esta sección contiene instrucciones para los autores de bibliotecas que realizan la compilación sobre el enrutamiento. Estos detalles están diseñados para garantizar que los desarrolladores de aplicaciones tengan una buena experiencia en el uso de bibliotecas y marcos que amplían el enrutamiento.

### <a name="define-endpoints"></a>Definición de puntos de conexión

Para crear un marco que use el enrutamiento para la coincidencia de direcciones URL, empiece por definir una experiencia de usuario que se base en <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.

**REALICE** la compilación sobre <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>. Esto permite a los usuarios crear el marco de trabajo con otras características de ASP.NET Core sin confusión. Todas las plantillas de ASP.NET Core incluyen el enrutamiento. Asuma que el enrutamiento está presente y es familiar para los usuarios.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**DEVUELVA** un tipo concreto sellado a partir de una llamada a `MapMyFramework(...)` que implemente <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>. La mayoría de los métodos `Map...` del marco siguen este patrón. La interfaz `IEndpointConventionBuilder`:

* Permite la composición de metadatos.
* Es el destino de diversos métodos de extensión.

La declaración de un tipo propio permite agregar funcionalidad específica del marco propia al generador. Es correcto encapsular un generador declarado por el marco y reenviarle llamadas.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**CONSIDERE LA POSIBILIDAD** de escribir un objeto <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> propio. `EndpointDataSource` es la primitiva de bajo nivel para declarar y actualizar una colección de puntos de conexión. `EndpointDataSource` es una API eficaz que usan los controladores y Razor Pages.

Las pruebas de enrutamiento tienen un [ejemplo básico](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) de un origen de datos que no es de actualización.

**NO** intente registrar un objeto `EndpointDataSource` de forma predeterminada. Exija a los usuarios que registren el marco en <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>. La filosofía del enrutamiento es que nada se incluye de forma predeterminada y que `UseEndpoints` es el lugar donde se registran los puntos de conexión.

### <a name="creating-routing-integrated-middleware"></a>Creación de middleware con enrutamiento integrada

**CONSIDERE LA POSIBILIDAD** de definir tipos de metadatos como una interfaz.

**PERMITA** el uso de los tipos de metadatos como atributo en clases y métodos.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Los marcos como los controladores y Razor Pages admiten la aplicación de atributos de metadatos a tipos y métodos. Si declara tipos de metadatos:

* Haga que sean accesibles como [atributos](/dotnet/csharp/programming-guide/concepts/attributes/).
* La mayoría de los usuarios están familiarizados con la aplicación de atributos.

La declaración de un tipo de metadatos como una interfaz agrega otro nivel de flexibilidad:

* Las interfaces admiten composición.
* Los desarrolladores pueden declarar tipos propios que combinen varias directivas.

**PERMITA** que los metadatos se puedan invalidar, como se muestra en el ejemplo siguiente:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

La mejor manera de seguir estas instrucciones es evitar la definición de **metadatos de marcador** :

* No busque solo la presencia de un tipo de metadatos.
* Defina una propiedad en los metadatos y compruébela.

La colección de metadatos está ordenada y admite la invalidación por prioridad. En el caso de los controladores, los metadatos del método de acción son más específicos.

**PERMITA** que el middleware sea útil con y sin el enrutamiento.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

Como ejemplo de esta instrucción, considere la posibilidad de usar el middleware `UseAuthorization`. El middleware de autorización permite pasar una directiva de reserva. <!-- shown where?  (shown here) --> La directiva de reserva, si se especifica, se aplica a:

* Puntos de conexión sin una directiva especificada.
* Solicitudes que no coinciden con un punto de conexión.

Esto hace que el middleware de autorización sea útil fuera del contexto del enrutamiento. El middleware de autorización se puede usar para la programación de middleware tradicional.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

El enrutamiento es responsable de asignar URI de solicitud a los puntos de conexión y de distribuir las solicitudes entrantes a esos puntos de conexión. Las rutas se definen en la aplicación y se configuran cuando se inicia la aplicación. Una ruta puede extraer opcionalmente valores de la dirección URL contenida en la solicitud, que se pueden usar para procesar las solicitudes. Con la información de ruta de la aplicación, el enrutamiento también puede generar direcciones URL que se asignan a puntos de conexión.

Para usar los escenarios de enrutamiento más recientes de ASP.NET Core 2.2, especifique la [versión de compatibilidad](xref:mvc/compatibility-version) en el registro de servicios de MVC en `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

La opción <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> determina si el enrutamiento debe usar de forma interna la lógica basada en el punto de conexión o la lógica basada en <xref:Microsoft.AspNetCore.Routing.IRouter> de ASP.NET Core 2.1 o una versión anterior. Cuando la versión de compatibilidad se establece en 2.2 o una versión posterior, el valor predeterminado es `true`. Establezca el valor en `false` para usar la lógica de enrutamiento anterior:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Para obtener más información sobre el enrutamiento basado en <xref:Microsoft.AspNetCore.Routing.IRouter>, vea la [versión para ASP.NET Core 2.1 de este tema](?view=aspnetcore-2.1).

> [!IMPORTANT]
> En este documento se describe el enrutamiento de ASP.NET Core de bajo nivel. Para obtener información sobre el enrutamiento de ASP.NET Core MVC, vea <xref:mvc/controllers/routing>. Para obtener más información sobre las convenciones de enrutamiento en Razor Pages, consulte <xref:razor-pages/razor-pages-conventions>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Fundamentos del enrutamiento

La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas. La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:

* Admite un esquema de enrutamiento básico y descriptivo.
* Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.

Los desarrolladores suelen agregar rutas breves adicionales a áreas de mucho tráfico de una aplicación en situaciones especializadas mediante el [enrutamiento de atributos](xref:mvc/controllers/routing#attribute-routing) o rutas convencionales dedicadas. Entre los ejemplos de situaciones especializadas se incluyen los puntos de conexión de blog y de comercio electrónico.

Las API web deben usar el enrutamiento mediante atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante verbos HTTP. Esto significa que muchas operaciones (por ejemplo, GET y POST) del mismo recurso lógico usan la misma dirección URL. El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.

En las aplicaciones de Razor Pages se usa el enrutamiento convencional predeterminado para proporcionar recursos con nombre en la carpeta *Páginas* de una aplicación. Existen convenciones adicionales que permiten personalizar el comportamiento de enrutamiento de Razor Pages. Para obtener más información, vea <xref:razor-pages/index> y <xref:razor-pages/razor-pages-conventions>.

La compatibilidad de la generación de direcciones URL permite desarrollar la aplicación sin codificar de forma rígida las direcciones URL para vincular la aplicación. Esta compatibilidad permite empezar con una configuración de enrutamiento básica y modificar las rutas una vez determinado el diseño de los recursos de la aplicación.

El enrutamiento usa *puntos de conexión* (`Endpoint`) para representar los puntos de conexión lógicos en una aplicación.

Un punto de conexión define un delegado para procesar las solicitudes y una colección de metadatos arbitrarios. Los metadatos se usan para implementar cuestiones transversales según las directivas y la configuración asociada a cada punto de conexión.

El sistema de enrutamiento tiene las características siguientes:

* La sintaxis de plantilla de ruta se usa para definir las rutas con parámetros de ruta con tokens.
* Se permite la configuración de puntos de conexión de estilo convencional y de estilo de atributo.
* Se usa <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> para determinar si un parámetro de dirección URL contiene un valor válido para una restricción de punto de conexión determinada.
* Los modelos de aplicación, como MVC y Razor Pages, registran todos sus puntos de conexión, que presentan una implementación predecible de los escenarios de enrutamiento.
* La implementación de enrutamiento toma decisiones relativas al enrutamiento siempre que sea lo deseado en la canalización de middleware.
* El middleware que aparece después de un middleware de enrutamiento puede inspeccionar el resultado de la decisión del punto de conexión del middleware de enrutamiento para un URI de solicitud determinado.
* Se pueden enumerar todos los puntos de conexión de la aplicación en cualquier parte de la canalización de middleware.
* Una aplicación puede usar el enrutamiento para generar direcciones URL (por ejemplo, para el redireccionamiento o los vínculos) en función de la información del punto de conexión. De este modo, se evita codificar de forma rígida las direcciones URL, lo que facilita el mantenimiento.
* La generación de direcciones URL se basa en direcciones, que admiten la extensibilidad arbitraria:

  * La API del generador de vínculos (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) se puede resolver en cualquier lugar mediante la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) para generar direcciones URL.
  * Cuando la API del generador de vínculos no está disponible a través de DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ofrece métodos para generar direcciones URL.

> [!NOTE]
> Con el lanzamiento del enrutamiento de punto de conexión en ASP.NET Core 2.2, la vinculación de punto de conexión se limita a acciones y páginas de Razor Pages y MVC. Las expansiones de las funciones de vinculación de punto de conexión están previstas para próximas versiones.

La clase <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> conecta el enrutamiento a la canalización de [software intermedio](xref:fundamentals/middleware/index). [ASP.NET Core MVC](xref:mvc/overview) agrega enrutamiento a la canalización de middleware como parte de su configuración y controla el enrutamiento en las aplicaciones de MVC y Razor Pages. Para obtener información sobre cómo usar el enrutamiento como componente independiente, vea la sección [Uso de software intermedio de enrutamiento](#use-routing-middleware).

### <a name="url-matching"></a>Coincidencia de dirección URL

La coincidencia de dirección URL es el proceso por el cual el enrutamiento envía una solicitud entrante a un *punto de conexión* . Este proceso se basa en datos de la ruta de dirección URL, pero se puede ampliar para tener en cuenta cualquier dato de la solicitud. La capacidad de enviar solicitudes a controladores independientes es clave para escalar el tamaño y la complejidad de una aplicación.

El sistema de enrutamiento en el enrutamiento de punto de conexión es responsable de todas las decisiones relativas al envío. Como el middleware aplica las directivas en función del punto de conexión seleccionado, es importante que cualquier decisión que pueda afectar a la distribución o la aplicación de directivas de seguridad se realice dentro del sistema de enrutamiento.

Cuando se ejecuta el delegado del punto de conexión, las propiedades de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) se establecen en los valores adecuados en función del procesamiento de solicitudes realizado hasta el momento.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) es un diccionario de los *valores de ruta* generados desde la ruta. Estos valores se suelen determinar mediante la conversión en tokens de la dirección URL, y se pueden usar para aceptar la entrada del usuario o para tomar otras decisiones sobre el envío dentro de la aplicación.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) es un contenedor de propiedades de datos adicionales relacionados con la ruta coincidente. Se proporcionan <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> para permitir la asociación de datos de estado con cada ruta, de modo que la aplicación pueda tomar decisiones en función de las rutas que han coincidido. Estos valores los define el desarrollador y **no** afectan de ninguna manera al comportamiento del enrutamiento. Además, los valores que se guardan provisionalmente en [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) pueden ser de cualquier tipo, a diferencia de [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), que deben poder convertirse fácilmente en cadenas y a partir de estas.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) es una lista de las rutas que han participado en encontrar una coincidencia correcta con la solicitud. Las rutas se pueden anidar unas dentro de otras. La propiedad <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> refleja la ruta de acceso del árbol lógico de rutas que han tenido como resultado una coincidencia. Por lo general, el primer elemento de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> es la colección de rutas y se debe usar para la generación de direcciones URL. El último elemento de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> es el controlador de ruta que ha coincidido.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generación de direcciones URL con LinkGenerator

La generación de dirección URL es el proceso por el cual el enrutamiento puede crear una ruta de dirección URL basada en un conjunto de valores de ruta. Esto permite una separación lógica entre los puntos de conexión y las direcciones URL que tienen acceso a ellos.

El enrutamiento de punto de conexión incluye la API del generador de vínculos (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator> es un servicio singleton que se puede recuperar a partir de la [DI](xref:fundamentals/dependency-injection). La API se puede usar fuera del contexto de una solicitud en ejecución. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> de MVC y los escenarios que dependen de <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, como los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), los de HTML y [los resultados de acción](xref:mvc/controllers/actions), usan el generador de vínculos para proporcionar funciones de generación de vínculos.

El generador de vínculos está respaldado por el concepto de una *dirección* y *esquemas de direcciones* . Un esquema de direcciones es una manera de determinar los puntos de conexión que se deben tener en cuenta para la generación de vínculos. Por ejemplo, los escenarios de nombre y valores de ruta de Razor Pages y MVC con los que muchos usuarios están familiarizados se implementan como un esquema de direcciones.

El generador de vínculos puede vincular a acciones y páginas de Razor Pages y MVC a través de los métodos de extensión siguientes:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Una sobrecarga de estos métodos acepta argumentos que incluyan `HttpContext`. Estos métodos son equivalentes funcionalmente a `Url.Action` y `Url.Page`, pero ofrecen flexibilidad y opciones adicionales.

Los métodos `GetPath*` son más similares a `Url.Action` y `Url.Page`, dado que generan un URI que contiene una ruta de acceso absoluta. Los métodos `GetUri*` siempre generan un URI absoluto que contiene un esquema y un host. Los métodos que aceptan `HttpContext` generan un URI en el contexto de la solicitud que se ejecuta. A menos que se reemplacen, se usan los valores de ruta de ambiente, la ruta de acceso base de la dirección URL, el esquema y el host de la solicitud que se ejecuta.

Se llama a <xref:Microsoft.AspNetCore.Routing.LinkGenerator> con una dirección. La generación de un URI se produce en dos pasos:

1. Se enlaza una dirección a una lista de puntos de conexión que coincidan con la dirección.
1. Se evalúa el elemento `RoutePattern` de cada punto de conexión hasta que se encuentra un patrón de ruta que coincida con los valores proporcionados. La salida resultante se combina con otras partes del URI proporcionadas al generador de vínculos y devueltas.

Los métodos proporcionados por <xref:Microsoft.AspNetCore.Routing.LinkGenerator> admiten funciones estándar de generación de vínculos para cualquier tipo de dirección. La forma más útil de usar el generador de vínculos es a través de métodos de extensión que realicen operaciones para un tipo de dirección específica.

| Método de extensión   | Descripción                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Genera un URI con una ruta de acceso absoluta en función de los valores proporcionados. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Genera un URI absoluto en función de los valores proporcionados.             |

> [!WARNING]
> Preste atención a las consecuencias siguientes de llamar a los métodos <xref:Microsoft.AspNetCore.Routing.LinkGenerator>:
>
> * Use los métodos de extensión `GetUri*` con precaución en una configuración de aplicación en la que no se valide el encabezado `Host` de las solicitudes entrantes. Si no se valida el encabezado `Host` de las solicitudes entrantes, la entrada de la solicitud que no sea de confianza se puede devolver al cliente en los URI de una página o vista. Se recomienda que todas las aplicaciones de producción configuren su servidor para validar el encabezado `Host` en función de valores válidos conocidos.
>
> * Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> con precaución en el middleware junto con `Map` o `MapWhen`. `Map*` cambia la ruta de acceso base de la solicitud que se ejecuta, lo que afecta a la salida de la generación de vínculos. Todas las API de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permiten especificar una ruta de acceso base. Especifique siempre una ruta de acceso base vacía para deshacer el efecto de `Map*` en la generación de vínculos.

## <a name="differences-from-earlier-versions-of-routing"></a>Diferencias con respecto a versiones anteriores del enrutamiento

Existen algunas diferencias entre el enrutamiento de punto de conexión de ASP.NET Core 2.2 o posterior, y las versiones anteriores del enrutamiento en ASP.NET Core:

* El sistema de enrutamiento de punto de conexión no es compatible con la extensibilidad basada en <xref:Microsoft.AspNetCore.Routing.IRouter>, incluida la herencia de <xref:Microsoft.AspNetCore.Routing.Route>.

* El enrutamiento de punto de conexión no es compatible con [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Utilice la [versión compatibilidad](xref:mvc/compatibility-version) 2.1 (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) para seguir usando la corrección de compatibilidad.

* El enrutamiento de punto de conexión tiene un comportamiento diferente para el uso de mayúsculas y minúsculas en los URI generados al usar rutas convencionales.

  Tenga en cuenta la plantilla de ruta predeterminada siguiente:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supongamos que genera un vínculo a una acción mediante la ruta siguiente:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Con el enrutamiento basado en <xref:Microsoft.AspNetCore.Routing.IRouter>, este código genera un URI de `/blog/ReadPost/17`, que respeta las mayúsculas y minúsculas del valor de ruta proporcionado. El enrutamiento de punto de conexión de ASP.NET Core 2.2 o versiones posteriores genera `/Blog/ReadPost/17` ("Blog" se pone mayúscula). El enrutamiento de punto de conexión proporciona la interfaz `IOutboundParameterTransformer`, que se puede usar para personalizar este comportamiento de forma global, o bien para aplicar otras convenciones para la asignación de direcciones URL.

  Para obtener más información, vea la sección [Referencia de transformadores de parámetros](#parameter-transformer-reference).

* La generación de vínculos que se usa en Razor Pages y MVC con las rutas convencionales tiene un comportamiento diferente al intentar vincularlo a un controlador o una acción, o a una página que no existe.

  Tenga en cuenta la plantilla de ruta predeterminada siguiente:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supongamos que genera un vínculo a una acción mediante la plantilla predeterminada con lo siguiente:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Con el enrutamiento basado en `IRouter`, el resultado siempre es `/Blog/ReadPost/17`, incluso aunque `BlogController` no exista o no tenga un método de acción `ReadPost`. Como se esperaba, si el método de acción existe, el enrutamiento de punto de conexión de ASP.NET Core 2.2 o versiones posteriores genera `/Blog/ReadPost/17`. *Sin embargo, si la acción no existe, el enrutamiento de punto de conexión genera una cadena vacía.* Conceptualmente, el enrutamiento de punto de conexión no supone que, si la acción no existe, sí que exista el punto de conexión.

* El *algoritmo de invalidación del valor de ambiente* de la generación de vínculos tiene otro comportamiento al usarlo con el enrutamiento de punto de conexión.

  La *invalidación del valor de ambiente* es el algoritmo que decide qué valores de ruta de la solicitud que se ejecuta actualmente (los valores de ambiente) se pueden usar en las operaciones de generación de vínculos. El enrutamiento convencional siempre invalida los valores de ruta adicionales al vincular a otra acción. El enrutamiento mediante atributos no tenía este comportamiento antes del lanzamiento de ASP.NET Core 2.2. En versiones anteriores de ASP.NET Core, los vínculos a otra acción que usara los mismos nombres de parámetro de ruta producían errores de generación de vínculo. En ASP.NET Core 2.2 o versiones posteriores, las dos formas de enrutamiento invalidan los valores cuando se vincula a otra acción.

  Considere el ejemplo siguiente de ASP.NET Core 2.1 o una versión anterior. Al vincular a otra acción (o a otra página), los valores de ruta se pueden reutilizar de formas no deseadas.

  En */Pages/Store/Product.cshtml* :

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  En */Pages/Login.cshtml* :

  ```cshtml
  @page "{id?}"
  ```

  Si el URI es `/Store/Product/18` en ASP.NET Core 2.1 o versiones anteriores, el vínculo que genera `@Url.Page("/Login")` en la página Store/Info es `/Login/18`. Se reutiliza el valor `id` de 18, aunque el destino del vínculo sea otro elemento totalmente distinto de la aplicación. El valor de ruta `id` en el contexto de la página `/Login` probablemente sea un valor de id. de usuario, no un valor de id. de producto de tienda.

  En el enrutamiento de punto de conexión con ASP.NET Core 2.2 o versiones posteriores, el resultado es `/Login`. Los valores de ambiente no se reutilizan cuando el destino vinculado es otra acción o página.

* Sintaxis de parámetro de ruta de ida y vuelta: las barras diagonales no se codifican cuando se usa una sintaxis de parámetro comodín de doble asterisco (`**`).

  Durante la generación de vínculos, el sistema de enrutamiento codifica el valor capturado en un parámetro comodín de doble asterisco (`**`; por ejemplo, `{**myparametername}`), excepto las barras diagonales. El comodín de doble asterisco es compatible con el enrutamiento basado en `IRouter` en ASP.NET Core 2.2 o versiones posteriores.

  La sintaxis de parámetro comodín de un único asterisco en versiones anteriores de ASP.NET Core (`{*myparametername}`) sigue siendo compatible y las barras diagonales se codifican.

  | Ruta              | Vínculo generado con<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (la barra diagonal se codifica)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Ejemplo de middleware

En el ejemplo siguiente, un middleware usa la API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> para crear el vínculo a un método de acción que enumera los productos de la tienda. El uso del generador de vínculos mediante su inserción en una clase y la llamada a `GenerateLink` está disponible para cualquier clase de una aplicación.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Creación de rutas

La mayoría de las aplicaciones crea rutas mediante una llamada a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> o a uno de los métodos de extensión similares definidos en <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Todos los métodos de extensión <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> crean una instancia de <xref:Microsoft.AspNetCore.Routing.Route> y la agregan a la colección de rutas.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> no acepta un parámetro de controlador de ruta. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> solo agrega las rutas que se controlan mediante <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Para obtener más información sobre el enrutamiento en MVC, vea <xref:mvc/controllers/routing>.

El código siguiente es un ejemplo de una llamada a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> usada por una definición de ruta típica de ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Esta plantilla coincide con una ruta de dirección URL y extrae los valores de ruta. Por ejemplo, la ruta de acceso `/Products/Details/17` genera los valores de ruta siguientes: `{ controller = Products, action = Details, id = 17 }`.

Los valores de ruta se determinan al dividir la ruta de dirección URL en segmentos y hacer coincidir cada segmento con el nombre del *parámetro de ruta* en la plantilla de ruta. Los parámetros de ruta tienen un nombre asignado. Para definir los parámetros, el nombre del parámetro se incluye entre llaves `{ ... }`.

La plantilla anterior también podría coincidir con la ruta de dirección URL `/` y generar los valores `{ controller = Home, action = Index }`. Esto se debe a que los parámetros de ruta `{controller}` y `{action}` tienen valores predeterminados y el parámetro de ruta `id` es opcional. Un signo igual (`=`) seguido de un valor después del nombre del parámetro de ruta define un valor predeterminado para el parámetro. Un signo de interrogación (`?`) después del nombre del parámetro de ruta define un parámetro opcional.

Los parámetros de ruta con un valor predeterminado *siempre* generan un valor de ruta cuando la ruta coincide. Los parámetros opcionales no generan un valor de ruta si no existe el segmento de ruta de dirección URL correspondiente. Vea la sección [Referencia de plantilla de ruta](#route-template-reference) para obtener una descripción detallada de los escenarios y la sintaxis de la plantilla de ruta.

En el ejemplo siguiente, la definición de parámetro de ruta `{id:int}` define una [restricción de ruta](#route-constraint-reference) para el parámetro de ruta `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Esta plantilla coincide con una ruta de dirección URL como `/Products/Details/17`, pero no con `/Products/Details/Apples`. Las restricciones de ruta implementan <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> e inspeccionan los valores de ruta para comprobarlos. En este ejemplo, el valor de ruta `id` debe poder convertirse en un entero. Vea la [Referencia de restricción de ruta](#route-constraint-reference) para obtener una explicación de las restricciones de ruta proporcionadas por el marco de trabajo.

Las sobrecargas adicionales de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> aceptan valores para `constraints`, `dataTokens` y `defaults`. Estos parámetros suelen usarse para pasar un objeto de tipo anónimo, donde los nombres de propiedad del tipo anónimo coinciden con los nombres de los parámetros de ruta.

En los ejemplos <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> siguientes se crean rutas equivalentes:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La sintaxis insertada para definir las restricciones y los valores predeterminados puede ser conveniente para las rutas simples. Pero hay algunos escenarios, como los tokens de datos, que no son compatibles con la sintaxis insertada.

En el ejemplo siguiente se muestran algunos escenarios más:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

La plantilla anterior coincide con una ruta de dirección URL como `/Blog/All-About-Routing/Introduction` y extrae los valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. La ruta genera los valores de ruta predeterminados para `controller` y `action`, incluso si no hay parámetros de ruta correspondientes en la plantilla. Los valores predeterminados pueden especificarse en la plantilla de ruta. El parámetro de ruta `article` se define como un *comodín* por la aparición de un asterisco doble (`**`) antes del nombre del parámetro de ruta. Los parámetros de ruta comodín capturan el resto de la ruta de dirección URL y también pueden coincidir con la cadena vacía.

En el ejemplo siguiente se agregan restricciones de ruta y tokens de datos:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

La plantilla anterior coincide con una ruta de dirección URL como `/en-US/Products/5` y extrae los valores `{ controller = Products, action = Details, id = 5 }` y los tokens de datos `{ locale = en-US }`.

![Tokens de Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generación de direcciones URL de clase de ruta

La clase <xref:Microsoft.AspNetCore.Routing.Route> también puede llevar a cabo la generación de dirección URL mediante la combinación de un conjunto de valores de ruta con su plantilla de ruta. Se trata lógicamente del proceso inverso de hacer coincidir la ruta de dirección URL.

> [!TIP]
> Para entender mejor la generación de direcciones URL, imagine qué dirección URL quiere generar y, después, piense cómo coincidiría una plantilla de ruta con esa dirección URL. ¿Qué valores se generarían? Este es un equivalente aproximado de cómo funciona la generación de dirección URL en la clase <xref:Microsoft.AspNetCore.Routing.Route>.

En el ejemplo siguiente se usa una ruta predeterminada de ASP.NET Core MVC general:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Con los valores de ruta `{ controller = Products, action = List }`, se genera la dirección URL `/Products/List`. Los valores de ruta se sustituyen por los parámetros de ruta correspondientes para formar la ruta de dirección URL. Como `id` es un parámetro de ruta opcional, la dirección URL se ha generado correctamente sin ningún valor para `id`.

Con los valores de ruta `{ controller = Home, action = Index }`, se genera la dirección URL `/`. Los valores de ruta proporcionados coinciden con los valores predeterminados, y los segmentos correspondientes a los valores predeterminados se omiten sin ningún riesgo.

Ambas direcciones URL generadas realizan un recorrido de ida y vuelta con la definición de ruta siguiente (`/Home/Index` y `/`) y generan los mismos valores de ruta que se usaron para generar la dirección URL.

> [!NOTE]
> Las aplicaciones con ASP.NET Core MVC deben usar <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> para generar direcciones URL en lugar de llamar directamente al enrutamiento.

Para obtener más información sobre la generación de direcciones URL, vea la sección [Referencia de generación de direcciones URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Uso de software intermedio de enrutamiento

Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) en el archivo de proyecto de la aplicación.

Agregue enrutamiento al contenedor de servicios en `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Las rutas se deben configurar en el método `Startup.Configure`. En la aplicación de ejemplo se usan las API siguientes:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: solo coincide con solicitudes HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

En la tabla siguiente se muestran las respuestas con los URI especificados.

| Identificador URI                    | Respuesta                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valores de ruta: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valores de ruta: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valores de ruta: [operation, track], [id, -3] |
| `/package/track/`      | La solicitud pasa; no hay ninguna coincidencia.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La solicitud pasa; solo coincide con HTTP GET. |
| `GET /hello/Joe/Smith` | La solicitud pasa; no hay ninguna coincidencia.              |

El marco de trabajo proporciona un conjunto de métodos de extensión para crear rutas (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Los métodos `Map[Verb]` usan restricciones para limitar la ruta al verbo HTTP en el nombre del método. Por ejemplo, vea <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> y <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referencia de plantilla de ruta

Los tokens entre llaves (`{ ... }`) definen *parámetros de ruta* que se enlazan si se encuentran coincidencias con la ruta. Puede definir más de un parámetro de ruta en un segmento de ruta, pero deben estar separados por un valor literal. Por ejemplo, `{controller=Home}{action=Index}` no es una ruta válida, ya que no hay ningún valor literal entre `{controller}` y `{action}`. Estos parámetros de ruta deben tener un nombre y, opcionalmente, atributos adicionales especificados.

El texto literal diferente de los parámetros de ruta (por ejemplo, `{id}`) y el separador de ruta `/` deben coincidir con el texto de la dirección URL. La coincidencia de texto no distingue mayúsculas de minúsculas y se basa en la representación descodificada de la ruta de las direcciones URL. Para que el delimitador de parámetro de ruta literal coincida (`{` o `}`), repita el carácter (`{{` o `}}`) para usarlo como secuencia de escape.

Los patrones de dirección URL que intentan capturar un nombre de archivo con una extensión de archivo opcional tienen consideraciones adicionales. Por ejemplo, considere la plantilla `files/{filename}.{ext?}`. Cuando existen valores para `filename` y `ext`, los dos valores se rellenan. Si solo existe un valor para `filename` en la dirección URL, la ruta coincide porque el punto final (`.`) es opcional. Las direcciones URL siguientes coinciden con esta ruta:

* `/files/myFile.txt`
* `/files/myFile`

Se puede usar un asterisco (`*`) o un asterisco doble (`**`) como prefijo de un parámetro de ruta para enlazar con el resto del URI. Se denominan parámetros *comodín* . Por ejemplo, `blog/{**slug}` coincide con cualquier URI que empiece por `/blog` y que vaya seguido de cualquier valor, que se asigna al valor de ruta `slug`. Los parámetros comodín también pueden coincidir con una cadena vacía.

El parámetro catch-all inserta los caracteres de escape correspondientes cuando se usa la ruta para generar una dirección URL, incluidos caracteres de separación de ruta de acceso (`/`). Por ejemplo, la ruta `foo/{*path}` con valores de ruta `{ path = "my/path" }` genera `foo/my%2Fpath`. Tenga en cuenta la barra diagonal de escape. Para los caracteres separadores de ruta de acceso de ida y vuelta, use el prefijo de parámetro de ruta `**`. La ruta `foo/{**path}` con `{ path = "my/path" }` genera `foo/my/path`.

Los parámetros de ruta pueden tener *valores predeterminados* designados mediante la especificación del valor predeterminado después del nombre de parámetro, separado por un signo igual (`=`). Por ejemplo, `{controller=Home}` define `Home` como el valor predeterminado de `controller`. El valor predeterminado se usa si no hay ningún valor en la dirección URL para el parámetro. Los parámetros de ruta se pueden convertir en opcionales si se anexa un signo de interrogación (`?`) al final del nombre del parámetro, como en `id?`. La diferencia entre los valores opcionales y los parámetros de ruta predeterminados es que un parámetro de ruta con un valor predeterminado siempre genera un valor, mientras que un parámetro opcional tiene un valor solo cuando la dirección URL de solicitud lo proporciona.

Los parámetros de ruta pueden tener restricciones que deben coincidir con el valor de ruta enlazado desde la dirección URL. Si se agrega un carácter de dos puntos (`:`) y un nombre de restricción después del nombre del parámetro de ruta, se especifica una *restricción insertada* en un parámetro de ruta. Si la restricción requiere argumentos, se incluyen entre paréntesis (`(...)`) después del nombre de restricción. Se pueden especificar varias restricciones insertadas si se anexa otro carácter de dos puntos (`:`) y un nombre de restricción.

El nombre de restricción y los argumentos se pasan al servicio <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para crear una instancia de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> para su uso en el procesamiento de direcciones URL. Por ejemplo, la plantilla de ruta `blog/{article:minlength(10)}` especifica una restricción `minlength` con el argumento `10`. Para obtener más información sobre las restricciones de ruta y una lista de las restricciones proporcionadas por el marco de trabajo, vea la sección [Referencia de restricciones de ruta](#route-constraint-reference).

Los parámetros de ruta también pueden tener transformadores de parámetros, que transforman el valor de un parámetro al generar vínculos y acciones y páginas coincidentes para las direcciones URL. Al igual que las restricciones, los transformadores de parámetros se pueden agregar en línea a un parámetro de ruta al incorporar un carácter de dos puntos (`:`) y un nombre de transformador después del nombre del parámetro de ruta. Por ejemplo, la plantilla de ruta `blog/{article:slugify}` especifica un transformador `slugify`. Para obtener más información sobre los transformadores de parámetros, vea la sección [Referencia de transformadores de parámetros](#parameter-transformer-reference).

En la tabla siguiente se muestran algunas plantillas de ruta de ejemplo y su comportamiento.

| Plantilla de ruta                           | URI coincidente de ejemplo    | El URI de la solicitud&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Solo coincide con la ruta de acceso única `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Coincide y establece `Page` en `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Coincide y establece `Page` en `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Se asigna al controlador `Products` y la acción `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Se asigna al controlador `Products` y la acción `Details` (`id` se establece en 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Se asigna al controlador `Home` y al método `Index` (`id` se omite).        |

El uso de una plantilla suele ser el método de enrutamiento más sencillo. Las restricciones y los valores predeterminados también se pueden especificar fuera de la plantilla de ruta.

> [!TIP]
> Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.

## <a name="reserved-routing-names"></a>Nombres de enrutamientos reservados

Las siguientes palabras clave son nombres reservados y no se pueden usar como nombres de ruta o parámetros:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Referencia de restricción de ruta

Las restricciones de ruta se ejecutan cuando se ha producido una coincidencia con la dirección URL entrante y la ruta de dirección URL se convierte en tokens en valores de ruta. En general, las restricciones de ruta inspeccionan el valor de ruta asociado a través de la plantilla de ruta y deciden si el valor es aceptable o no. Algunas restricciones de ruta usan datos ajenos al valor de ruta para decidir si la solicitud se puede enrutar. Por ejemplo, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> puede aceptar o rechazar una solicitud basada en su verbo HTTP. Las restricciones se usan en las solicitudes de enrutamiento y la generación de vínculos.

> [!WARNING]
> No use las restricciones para las **validación de entrada** . Si las restricciones se usan para la **validación de entrada** , los resultados de entrada no válidos producirán un error *404 - No encontrado* en lugar de un error *400 - Solicitud incorrecta* con un mensaje de error adecuado. Las restricciones de ruta se usan para **eliminar la ambigüedad** entre rutas similares, no para validar las entradas de una ruta determinada.

En la tabla siguiente se muestran algunas restricciones de ruta de ejemplo y su comportamiento esperado.

| Restricción | Ejemplo | Coincidencias de ejemplo | Notas |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | Coincide con cualquier entero.|
| `bool` | `{active:bool}` | `true`, `FALSE` | Coincide con `true` o `false`. No distingue mayúsculas de minúsculas.|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Coincide con un valor `DateTime` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Coincide con un valor `decimal` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Coincide con un valor `double` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Coincide con un valor `float` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Coincide con un valor `Guid` válido.|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Coincide con un valor `long` válido.|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La cadena debe tener al menos 4 caracteres.|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La cadena tiene 8 caracteres como máximo.|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La cadena debe tener una longitud exacta de 12 caracteres.|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La cadena debe tener al menos 8 caracteres y 16 como máximo.|
| `min(value)` | `{age:min(18)}` | `19` | El valor entero debe ser como mínimo 18.|
| `max(value)` | `{age:max(120)}` | `91` | Valor entero máximo de 120.|
| `range(min,max)` | `{age:range(18,120)}` | `91` | El valor entero debe ser como mínimo 18 y máximo 120.|
| `alpha` | `{name:alpha}` | `Rick` | La cadena debe constar de uno o más caracteres alfabéticos `a`-`z`. No distingue mayúsculas de minúsculas.|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La cadena debe coincidir con la expresión regular. Vea las sugerencias sobre cómo definir una expresión regular.|
| `required` | `{name:required}` | `Rick` | Se usa para exigir que un valor que no es de parámetro esté presente durante la generación de direcciones URL.|

Es posible aplicar varias restricciones delimitadas por dos puntos a un único parámetro. Por ejemplo, la siguiente restricción permite limitar un parámetro a un valor entero de 1 o superior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o `DateTime`) usan siempre la referencia cultural invariable. Estas restricciones dan por supuesto que la dirección URL no es localizable. Las restricciones de ruta proporcionadas por el marco de trabajo no modifican los valores almacenados en los valores de ruta. Todos los valores de ruta analizados desde la dirección URL se almacenan como cadenas. Por ejemplo, la restricción `float` intenta convertir el valor de ruta en un valor Float, pero el valor convertido se usa exclusivamente para comprobar que se puede convertir en Float.

## <a name="regular-expressions"></a>Expresiones regulares

El marco de trabajo de ASP.NET Core agrega `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` al constructor de expresiones regulares. Vea <xref:System.Text.RegularExpressions.RegexOptions> para obtener una descripción de estos miembros.

Las expresiones regulares usan delimitadores y tokens similares a los que usan el enrutamiento y el lenguaje C#. Es necesario usar secuencias de escape con los tokens de expresiones regulares. Para usar la expresión regular `^\d{3}-\d{2}-\d{4}$` en el enrutamiento:

* La expresión debe tener los caracteres de barra diagonal inversa `\` única proporcionados en la cadena como caracteres de barra diagonal inversa `\\` doble en el código fuente.
* La expresión regular debe requerir `\\` para aplicar la secuencia de escape el carácter de escape de cadena `\`.
* La expresión regular no requiere `\\` al usar [literales de cadena textual](/dotnet/csharp/language-reference/keywords/string).

Para aplicar secuencias de escape a los caracteres delimitadores de parámetro de enrutamiento `{`, `}`, `[` y `]`, duplique los caracteres en la expresión `{{`, `}`, `[[` y `]]`. En la tabla siguiente se muestra una expresión regular y la versión con la secuencia de escape:

| Expresión regular    | Expresión regular con secuencia de escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Las expresiones regulares que se usan en el enrutamiento suelen empezar con el carácter de acento circunflejo `^` y coinciden con la posición inicial de la cadena. Las expresiones suelen terminar con el carácter del signo de dólar `$` y coincidir con el final de la cadena. Los caracteres `^` y `$` garantizan que la expresión regular coincide con el valor completo del parámetro de ruta. Sin los caracteres `^` y `$`, la expresión regular coincide con cualquier subcadena de la cadena, algo que normalmente no es deseable. En la tabla siguiente se proporcionan ejemplos y se explica por qué coinciden o no.

| Expresión   | String    | Coincidir con | Comentario               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | 123abc456 | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | mz        | Sí   | Coincide con la expresión    |
| `[a-z]{2}`   | MZ        | Sí   | No distingue mayúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | No    | Vea `^` y `$` más arriba |
| `^[a-z]{2}$` | 123abc456 | No    | Vea `^` y `$` más arriba |

Para obtener más información sobre la sintaxis de expresiones regulares, vea [Expresiones regulares de .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir un parámetro a un conjunto conocido de valores posibles, use una expresión regular. Por ejemplo, `{action:regex(^(list|get|create)$)}` solo hace coincidir el valor de ruta `action` con `list`, `get` o `create`. Si se pasa al diccionario de restricciones, la cadena `^(list|get|create)$` es equivalente. Las restricciones que se pasan al diccionario de restricciones (no insertado en una plantilla) que no coinciden con una de las restricciones conocidas también se tratan como expresiones regulares.

## <a name="custom-route-constraints"></a>Restricciones de ruta personalizadas

Además de las restricciones de ruta integradas, se pueden crear restricciones de ruta personalizadas implementando la interfaz <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. La interfaz <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contiene un único método, `Match`, que devuelve `true` si se cumple la restricción, y `false` en caso contrario.

Para utilizar una restricción <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personalizada, el tipo de restricción de ruta debe registrarse con el parámetro <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de la aplicación en el contenedor de servicios de la aplicación. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> es un diccionario que asigna claves de restricciones de ruta a implementaciones de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> que validen esas restricciones. El parámetro <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de una aplicación puede actualizarse en `Startup.ConfigureServices` como parte de una llamada a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) o configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> directamente con `services.Configure<RouteOptions>`. Por ejemplo:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La restricción puede aplicarse a las rutas de la manera habitual, usando el nombre especificado al registrar el tipo de restricción. Por ejemplo:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Referencia de transformadores de parámetros

Transformadores de parámetros:

* Se ejecutan al generar un vínculo para <xref:Microsoft.AspNetCore.Routing.Route>.
* Implemente `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Se configuran con <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Toman el valor de ruta del parámetro y lo transforman en un nuevo valor de cadena.
* Como resultado, el valor transformado se usa en el vínculo generado.

Por ejemplo, un transformador de parámetros personalizado `slugify` en el patrón de ruta `blog\{article:slugify}` con `Url.Action(new { article = "MyTestArticle" })` genera `blog\my-test-article`.

Para usar un transformador de parámetros en un patrón de ruta, configúrelo primero con <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> en `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

El marco usa los transformadores de parámetros para transformar el URI en el que se resuelve un punto de conexión. Por ejemplo, ASP.NET Core MVC usa transformadores de parámetros para transformar el valor de ruta usado para hacer coincidir elementos `area`, `controller`, `action` y `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Con la ruta anterior, la acción `SubscriptionManagementController.GetAll` coincide con el URI `/subscription-management/get-all`. Un transformador de parámetros no cambia los valores de ruta usados para generar un vínculo. Por ejemplo, `Url.Action("GetAll", "SubscriptionManagement")` genera `/subscription-management/get-all`.

ASP.NET Core proporciona las convenciones de API para usar transformadores de parámetros con rutas generadas:

* ASP.NET Core MVC tiene la convención de API `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention`. Esta convención aplica un transformador de parámetro especificado a todas las rutas de atributo de la aplicación. El transformador de parámetro transforma los tokens de rutas de atributo a medida que se reemplazan. Para más información, consulte [Usar un transformador de parámetro para personalizar el reemplazo de tokens](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` Pages tiene la convención de API Razor. Esta convención aplica un transformador de parámetros especificado a todas las instancias de Razor Pages detectadas de forma automática. El transformador de parámetros transforma los segmentos de nombre de archivo y carpeta de las rutas de Razor Pages. Para más información, consulte el artículo sobre cómo [usar un transformador de parámetro para personalizar rutas de Razor Pages](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Referencia de generación de direcciones URL

En el ejemplo siguiente se muestra cómo se genera un vínculo a una ruta, dado un diccionario de valores de ruta y un valor <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

El valor <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generado al final del ejemplo anterior es `/package/create/123`. El diccionario proporciona los valores de ruta `operation` e `id` de la plantilla "Ruta de paquete de seguimiento", `package/{operation}/{id}`. Para obtener más información, vea el código de ejemplo de la sección [Uso de software intermedio de enrutamiento](#use-routing-middleware) o la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

El segundo parámetro del constructor <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> es una colección de *valores de ambiente* . Los valores de ambiente son adecuados porque limitan el número de valores que el desarrollador debe especificar dentro de un contexto de solicitud. Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En la acción `About` de `HomeController` de una aplicación ASP.NET Core MVC, no es necesario especificar el valor de ruta de controlador para vincular a la acción `Index` (se usará el valor de ambiente `Home`).

Los valores de ambiente que no coincidan con un parámetro se omiten. También se omiten los valores de ambiente cuando un valor proporcionado de forma explícita invalida el valor de ambiente. La coincidencia se produce de izquierda a derecha en la dirección URL.

Los valores que se proporcionan de forma explícita pero que no coinciden con un segmento de la ruta se agregan a la cadena de consulta. En la tabla siguiente se muestra el resultado cuando se usa la plantilla de ruta `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Resultado                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si una ruta tiene un valor predeterminado que no se corresponde con un parámetro y ese valor se proporciona de forma explícita, debe coincidir con el valor predeterminado:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La generación de vínculos solo genera un vínculo para esta ruta si se proporcionan los valores coincidentes para `controller` y `action`.

## <a name="complex-segments"></a>Segmentos complejos

Los segmentos complejos (por ejemplo, `[Route("/x{token}y")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo. Consulte [este código](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) para obtener una explicación detallada de cómo se comparan los segmentos complejos. El [código de ejemplo](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) no se usa en ASP.NET Core, pero proporciona una buena explicación de los segmentos complejos.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

El enrutamiento es responsable de asignar los URI de solicitud a los controladores de ruta y de distribuir las solicitudes entrantes. Las rutas se definen en la aplicación y se configuran cuando se inicia la aplicación. Una ruta puede extraer opcionalmente valores de la dirección URL contenida en la solicitud, que se pueden usar para procesar las solicitudes. Mediante las rutas configuradas de la aplicación, el enrutamiento puede generar direcciones URL que se asignan a los controladores de ruta.

Para usar los escenarios de enrutamiento más recientes de ASP.NET Core 2.1, especifique la [versión de compatibilidad](xref:mvc/compatibility-version) en el registro de servicios de MVC en `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> En este documento se describe el enrutamiento de ASP.NET Core de bajo nivel. Para obtener información sobre el enrutamiento de ASP.NET Core MVC, vea <xref:mvc/controllers/routing>. Para obtener más información sobre las convenciones de enrutamiento en Razor Pages, consulte <xref:razor-pages/razor-pages-conventions>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Fundamentos del enrutamiento

La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas. La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:

* Admite un esquema de enrutamiento básico y descriptivo.
* Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.

Los desarrolladores suelen agregar rutas breves adicionales a áreas de mucho tráfico de una aplicación en situaciones especializadas (por ejemplo, puntos de conexión de blog y comercio electrónico) con el [enrutamiento mediante atributos](xref:mvc/controllers/routing#attribute-routing) o rutas convencionales dedicadas.

Las API web deben usar el enrutamiento mediante atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante verbos HTTP. Esto significa que muchas operaciones (por ejemplo, GET y POST) del mismo recurso lógico usarán la misma dirección URL. El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.

En las aplicaciones de Razor Pages se usa el enrutamiento convencional predeterminado para proporcionar recursos con nombre en la carpeta *Páginas* de una aplicación. Existen convenciones adicionales que permiten personalizar el comportamiento de enrutamiento de Razor Pages. Para obtener más información, vea <xref:razor-pages/index> y <xref:razor-pages/razor-pages-conventions>.

La compatibilidad de la generación de direcciones URL permite desarrollar la aplicación sin codificar de forma rígida las direcciones URL para vincular la aplicación. Esta compatibilidad permite empezar con una configuración de enrutamiento básica y modificar las rutas una vez determinado el diseño de los recursos de la aplicación.

El enrutamiento usa implementaciones de ruta de <xref:Microsoft.AspNetCore.Routing.IRouter> para:

* Asignar las solicitudes entrantes a *controladores de ruta* .
* Generar las direcciones URL que se usan en las respuestas.

De forma predeterminada, una aplicación tiene una sola colección de rutas. Cuando llega una solicitud, las rutas de la colección se procesan en el orden en el que se encuentran en la colección. El marco de trabajo intenta hacer coincidir una dirección URL de solicitud entrante con una ruta de la colección mediante una llamada al método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> en cada ruta de la colección. Una respuesta puede usar el enrutamiento para generar direcciones URL (por ejemplo, para el redireccionamiento o los vínculos) en función de la información de ruta. De este modo, se evita codificar de forma rígida las direcciones URL, lo que facilita el mantenimiento.

El sistema de enrutamiento tiene las características siguientes:

* La sintaxis de plantilla de ruta se usa para definir las rutas con parámetros de ruta con tokens.
* Se permite la configuración de puntos de conexión de estilo convencional y de estilo de atributo.
* Se usa <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> para determinar si un parámetro de dirección URL contiene un valor válido para una restricción de punto de conexión determinada.
* Los modelos de aplicación, como MVC y Razor Pages, registran todas sus rutas, que tienen una implementación predecible de los escenarios de enrutamiento.
* Una respuesta puede usar el enrutamiento para generar direcciones URL (por ejemplo, para el redireccionamiento o los vínculos) en función de la información de ruta. De este modo, se evita codificar de forma rígida las direcciones URL, lo que facilita el mantenimiento.
* La generación de direcciones URL se basa en rutas, que admiten la extensibilidad arbitraria. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ofrece métodos para generar direcciones URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
La clase <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> conecta el enrutamiento a la canalización de [software intermedio](xref:fundamentals/middleware/index). [ASP.NET Core MVC](xref:mvc/overview) agrega enrutamiento a la canalización de middleware como parte de su configuración y controla el enrutamiento en las aplicaciones de MVC y Razor Pages. Para obtener información sobre cómo usar el enrutamiento como componente independiente, vea la sección [Uso de software intermedio de enrutamiento](#use-routing-middleware).

### <a name="url-matching"></a>Coincidencia de dirección URL

La coincidencia de dirección URL es el proceso por el cual el enrutamiento envía una solicitud entrante a un *controlador* . Este proceso se basa en datos de la ruta de dirección URL, pero se puede ampliar para tener en cuenta cualquier dato de la solicitud. La capacidad de enviar solicitudes a controladores independientes es clave para escalar el tamaño y la complejidad de una aplicación.

Las solicitudes entrantes especifican la clase <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, que llama al método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> en cada ruta de la secuencia. La instancia de <xref:Microsoft.AspNetCore.Routing.IRouter> decide si *controla* la solicitud mediante el establecimiento de [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) en un <xref:Microsoft.AspNetCore.Http.RequestDelegate> que no sea NULL. Si una ruta establece un controlador para la solicitud, el procesamiento de rutas se detiene y se invoca el controlador para procesar la solicitud. Si no se encuentra ningún controlador de ruta para procesar la solicitud, el middleware entrega la solicitud al siguiente middleware en la canalización de solicitudes.

La entrada principal para <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> es el [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) asociado a la solicitud actual. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) y [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) son salidas que se establecen después de que una ruta coincida.

Una coincidencia que llama a <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> también establece las propiedades de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) en los valores adecuados en función del procesamiento de solicitudes realizado hasta el momento.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) es un diccionario de los *valores de ruta* generados desde la ruta. Estos valores se suelen determinar mediante la conversión en tokens de la dirección URL, y se pueden usar para aceptar la entrada del usuario o para tomar otras decisiones sobre el envío dentro de la aplicación.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) es un contenedor de propiedades de datos adicionales relacionados con la ruta coincidente. Se proporcionan <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> para permitir la asociación de datos de estado con cada ruta, de modo que la aplicación pueda tomar decisiones en función de las rutas que han coincidido. Estos valores los define el desarrollador y **no** afectan de ninguna manera al comportamiento del enrutamiento. Además, los valores que se guardan provisionalmente en [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) pueden ser de cualquier tipo, a diferencia de [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), que deben poder convertirse fácilmente en cadenas y a partir de estas.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) es una lista de las rutas que han participado en encontrar una coincidencia correcta con la solicitud. Las rutas se pueden anidar unas dentro de otras. La propiedad <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> refleja la ruta de acceso del árbol lógico de rutas que han tenido como resultado una coincidencia. Por lo general, el primer elemento de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> es la colección de rutas y se debe usar para la generación de direcciones URL. El último elemento de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> es el controlador de ruta que ha coincidido.

<a name="lg"></a>

### <a name="url-generation"></a>Generación de dirección URL

La generación de dirección URL es el proceso por el cual el enrutamiento puede crear una ruta de dirección URL basada en un conjunto de valores de ruta. Esto permite una separación lógica entre los controladores de ruta y las direcciones URL que tienen acceso a ellos.

La generación de direcciones URL sigue un proceso iterativo similar, pero se inicia cuando el código de usuario o de marco de trabajo llama al método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de la colección de rutas. Se llama en secuencia al método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de cada *ruta* hasta que se devuelva un valor <xref:Microsoft.AspNetCore.Routing.VirtualPathData> distinto de NULL.

La principal entradas de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> son:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Las rutas usan principalmente los valores de ruta proporcionados por <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> y <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> para decidir si es posible generar una dirección URL y qué valores se van a incluir. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> son el conjunto de valores de ruta producidos a partir de la coincidencia con la solicitud actual. En cambio, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> son los valores de ruta que especifican cómo se genera la dirección URL deseada para la operación actual. Se proporciona <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> por si una ruta debe obtener servicios o datos adicionales asociados con el contexto actual.

> [!TIP]
> Piense en [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) como un conjunto de invalidaciones para [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). La generación de direcciones URL intenta reutilizar los valores de ruta de la solicitud actual para generar direcciones URL para los vínculos con la misma ruta o valores de ruta.

La salida de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> es <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> es un valor paralelo de <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> contiene el valor <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> de la dirección URL de salida y algunas propiedades más que la ruta debe establecer.

La propiedad [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) contiene la *ruta de acceso virtual* generada por la ruta. Es posible que deba procesar aún más la ruta de acceso, según sus necesidades. Si quiere representar la dirección URL generada en HTML, anteponga la ruta de acceso base de la aplicación.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) es una referencia a la ruta que ha generado correctamente la dirección URL.

La propiedad [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) es un diccionario de datos adicionales relacionados con la ruta que ha generado la dirección URL. Se trata del valor paralelo de [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Creación de rutas

El enrutamiento proporciona la clase <xref:Microsoft.AspNetCore.Routing.Route> como implementación estándar de <xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route> usa la sintaxis de *plantilla de ruta* para definir patrones que se hacen coincidir con la ruta de dirección URL cuando se llama a <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>. <xref:Microsoft.AspNetCore.Routing.Route> usa la misma plantilla de ruta para generar una dirección URL cuando se llama a <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>.

La mayoría de las aplicaciones crea rutas mediante una llamada a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> o a uno de los métodos de extensión similares definidos en <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Todos los métodos de extensión <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> crean una instancia de <xref:Microsoft.AspNetCore.Routing.Route> y la agregan a la colección de rutas.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> no acepta un parámetro de controlador de ruta. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> solo agrega las rutas que se controlan mediante <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. El controlador predeterminado es un elemento `IRouter`, y es posible que el controlador no pueda atender la solicitud. Por ejemplo, ASP.NET Core MVC se suele configurar como un controlador predeterminado que solo controla las solicitudes que coinciden con un controlador y una acción disponibles. Para obtener más información sobre el enrutamiento en MVC, vea <xref:mvc/controllers/routing>.

El código siguiente es un ejemplo de una llamada a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> usada por una definición de ruta típica de ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Esta plantilla coincide con una ruta de dirección URL y extrae los valores de ruta. Por ejemplo, la ruta de acceso `/Products/Details/17` genera los valores de ruta siguientes: `{ controller = Products, action = Details, id = 17 }`.

Los valores de ruta se determinan al dividir la ruta de dirección URL en segmentos y hacer coincidir cada segmento con el nombre del *parámetro de ruta* en la plantilla de ruta. Los parámetros de ruta tienen un nombre asignado. Para definir los parámetros, el nombre del parámetro se incluye entre llaves `{ ... }`.

La plantilla anterior también podría coincidir con la ruta de dirección URL `/` y generar los valores `{ controller = Home, action = Index }`. Esto se debe a que los parámetros de ruta `{controller}` y `{action}` tienen valores predeterminados y el parámetro de ruta `id` es opcional. Un signo igual (`=`) seguido de un valor después del nombre del parámetro de ruta define un valor predeterminado para el parámetro. Un signo de interrogación (`?`) después del nombre del parámetro de ruta define un parámetro opcional.

Los parámetros de ruta con un valor predeterminado *siempre* generan un valor de ruta cuando la ruta coincide. Los parámetros opcionales no generan un valor de ruta si no existe el segmento de ruta de dirección URL correspondiente. Vea la sección [Referencia de plantilla de ruta](#route-template-reference) para obtener una descripción detallada de los escenarios y la sintaxis de la plantilla de ruta.

En el ejemplo siguiente, la definición de parámetro de ruta `{id:int}` define una [restricción de ruta](#route-constraint-reference) para el parámetro de ruta `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Esta plantilla coincide con una ruta de dirección URL como `/Products/Details/17`, pero no con `/Products/Details/Apples`. Las restricciones de ruta implementan <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> e inspeccionan los valores de ruta para comprobarlos. En este ejemplo, el valor de ruta `id` debe poder convertirse en un entero. Vea la [Referencia de restricción de ruta](#route-constraint-reference) para obtener una explicación de las restricciones de ruta proporcionadas por el marco de trabajo.

Las sobrecargas adicionales de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> aceptan valores para `constraints`, `dataTokens` y `defaults`. Estos parámetros suelen usarse para pasar un objeto de tipo anónimo, donde los nombres de propiedad del tipo anónimo coinciden con los nombres de los parámetros de ruta.

En los ejemplos <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> siguientes se crean rutas equivalentes:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La sintaxis insertada para definir las restricciones y los valores predeterminados puede ser conveniente para las rutas simples. Pero hay algunos escenarios, como los tokens de datos, que no son compatibles con la sintaxis insertada.

En el ejemplo siguiente se muestran algunos escenarios más:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

La plantilla anterior coincide con una ruta de dirección URL como `/Blog/All-About-Routing/Introduction` y extrae los valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. La ruta genera los valores de ruta predeterminados para `controller` y `action`, incluso si no hay parámetros de ruta correspondientes en la plantilla. Los valores predeterminados pueden especificarse en la plantilla de ruta. El parámetro de ruta `article` se define como un *comodín* por la aparición de un asterisco (`*`) antes del nombre del parámetro de ruta. Los parámetros de ruta comodín capturan el resto de la ruta de dirección URL y también pueden coincidir con la cadena vacía.

En el ejemplo siguiente se agregan restricciones de ruta y tokens de datos:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

La plantilla anterior coincide con una ruta de dirección URL como `/en-US/Products/5` y extrae los valores `{ controller = Products, action = Details, id = 5 }` y los tokens de datos `{ locale = en-US }`.

![Tokens de Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generación de direcciones URL de clase de ruta

La clase <xref:Microsoft.AspNetCore.Routing.Route> también puede llevar a cabo la generación de dirección URL mediante la combinación de un conjunto de valores de ruta con su plantilla de ruta. Se trata lógicamente del proceso inverso de hacer coincidir la ruta de dirección URL.

> [!TIP]
> Para entender mejor la generación de direcciones URL, imagine qué dirección URL quiere generar y, después, piense cómo coincidiría una plantilla de ruta con esa dirección URL. ¿Qué valores se generarían? Este es un equivalente aproximado de cómo funciona la generación de dirección URL en la clase <xref:Microsoft.AspNetCore.Routing.Route>.

En el ejemplo siguiente se usa una ruta predeterminada de ASP.NET Core MVC general:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Con los valores de ruta `{ controller = Products, action = List }`, se genera la dirección URL `/Products/List`. Los valores de ruta se sustituyen por los parámetros de ruta correspondientes para formar la ruta de dirección URL. Como `id` es un parámetro de ruta opcional, la dirección URL se ha generado correctamente sin ningún valor para `id`.

Con los valores de ruta `{ controller = Home, action = Index }`, se genera la dirección URL `/`. Los valores de ruta proporcionados coinciden con los valores predeterminados, y los segmentos correspondientes a los valores predeterminados se omiten sin ningún riesgo.

Ambas direcciones URL generadas realizan un recorrido de ida y vuelta con la definición de ruta siguiente (`/Home/Index` y `/`) y generan los mismos valores de ruta que se usaron para generar la dirección URL.

> [!NOTE]
> Las aplicaciones con ASP.NET Core MVC deben usar <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> para generar direcciones URL en lugar de llamar directamente al enrutamiento.

Para obtener más información sobre la generación de direcciones URL, vea la sección [Referencia de generación de direcciones URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Uso de middleware de enrutamiento

Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) en el archivo de proyecto de la aplicación.

Agregue enrutamiento al contenedor de servicios en `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Las rutas se deben configurar en el método `Startup.Configure`. En la aplicación de ejemplo se usan las API siguientes:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: solo coincide con solicitudes HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

En la tabla siguiente se muestran las respuestas con los URI especificados.

| Identificador URI                    | Respuesta                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valores de ruta: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valores de ruta: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valores de ruta: [operation, track], [id, -3] |
| `/package/track/`      | La solicitud pasa; no hay ninguna coincidencia.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La solicitud pasa; solo coincide con HTTP GET. |
| `GET /hello/Joe/Smith` | La solicitud pasa; no hay ninguna coincidencia.              |

Si va a configurar una única ruta, pase una instancia de `IRouter` para llamar a <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>. No tendrá que usar <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

El marco de trabajo proporciona un conjunto de métodos de extensión para crear rutas (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Algunos de los métodos enumerados, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, requieren un <xref:Microsoft.AspNetCore.Http.RequestDelegate>. El valor <xref:Microsoft.AspNetCore.Http.RequestDelegate> se usa como *controlador de ruta* cuando la ruta coincida. Otros métodos de esta familia permiten configurar una canalización de software intermedio para usarla como controlador de ruta. Si el método `Map*` no acepta un controlador, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, usa <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

Los métodos `Map[Verb]` usan restricciones para limitar la ruta al verbo HTTP en el nombre del método. Por ejemplo, vea <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> y <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referencia de plantilla de ruta

Los tokens entre llaves (`{ ... }`) definen *parámetros de ruta* que se enlazan si se encuentran coincidencias con la ruta. Puede definir más de un parámetro de ruta en un segmento de ruta, pero deben estar separados por un valor literal. Por ejemplo, `{controller=Home}{action=Index}` no es una ruta válida, ya que no hay ningún valor literal entre `{controller}` y `{action}`. Estos parámetros de ruta deben tener un nombre y, opcionalmente, atributos adicionales especificados.

El texto literal diferente de los parámetros de ruta (por ejemplo, `{id}`) y el separador de ruta `/` deben coincidir con el texto de la dirección URL. La coincidencia de texto no distingue mayúsculas de minúsculas y se basa en la representación descodificada de la ruta de las direcciones URL. Para que el delimitador de parámetro de ruta literal coincida (`{` o `}`), repita el carácter (`{{` o `}}`) para usarlo como secuencia de escape.

Los patrones de dirección URL que intentan capturar un nombre de archivo con una extensión de archivo opcional tienen consideraciones adicionales. Por ejemplo, considere la plantilla `files/{filename}.{ext?}`. Cuando existen valores para `filename` y `ext`, los dos valores se rellenan. Si solo existe un valor para `filename` en la dirección URL, la ruta coincide porque el punto final (`.`) es opcional. Las direcciones URL siguientes coinciden con esta ruta:

* `/files/myFile.txt`
* `/files/myFile`

Se puede usar el asterisco (`*`) como prefijo de un parámetro de ruta para enlazar con el resto del URI. Es lo que se denomina un parámetro *comodín* . Por ejemplo, `blog/{*slug}` coincide con cualquier URI que empiece por `/blog` y que vaya seguido de cualquier valor, que se asigna al valor de ruta `slug`. Los parámetros comodín también pueden coincidir con una cadena vacía.

El parámetro catch-all inserta los caracteres de escape correspondientes cuando se usa la ruta para generar una dirección URL, incluidos caracteres de separación de ruta de acceso (`/`). Por ejemplo, la ruta `foo/{*path}` con valores de ruta `{ path = "my/path" }` genera `foo/my%2Fpath`. Tenga en cuenta la barra diagonal de escape.

Los parámetros de ruta pueden tener *valores predeterminados* designados mediante la especificación del valor predeterminado después del nombre de parámetro, separado por un signo igual (`=`). Por ejemplo, `{controller=Home}` define `Home` como el valor predeterminado de `controller`. El valor predeterminado se usa si no hay ningún valor en la dirección URL para el parámetro. Los parámetros de ruta se pueden convertir en opcionales si se anexa un signo de interrogación (`?`) al final del nombre del parámetro, como en `id?`. La diferencia entre los valores opcionales y los parámetros de ruta predeterminados es que un parámetro de ruta con un valor predeterminado siempre genera un valor, mientras que un parámetro opcional tiene un valor solo cuando la dirección URL de solicitud lo proporciona.

Los parámetros de ruta pueden tener restricciones que deben coincidir con el valor de ruta enlazado desde la dirección URL. Si se agrega un carácter de dos puntos (`:`) y un nombre de restricción después del nombre del parámetro de ruta, se especifica una *restricción insertada* en un parámetro de ruta. Si la restricción requiere argumentos, se incluyen entre paréntesis (`(...)`) después del nombre de restricción. Se pueden especificar varias restricciones insertadas si se anexa otro carácter de dos puntos (`:`) y un nombre de restricción.

El nombre de restricción y los argumentos se pasan al servicio <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para crear una instancia de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> para su uso en el procesamiento de direcciones URL. Por ejemplo, la plantilla de ruta `blog/{article:minlength(10)}` especifica una restricción `minlength` con el argumento `10`. Para obtener más información sobre las restricciones de ruta y una lista de las restricciones proporcionadas por el marco de trabajo, vea la sección [Referencia de restricciones de ruta](#route-constraint-reference).

En la tabla siguiente se muestran algunas plantillas de ruta de ejemplo y su comportamiento.

| Plantilla de ruta                           | URI coincidente de ejemplo    | El URI de la solicitud&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Solo coincide con la ruta de acceso única `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Coincide y establece `Page` en `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Coincide y establece `Page` en `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Se asigna al controlador `Products` y la acción `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Se asigna al controlador `Products` y la acción `Details` (`id` se establece en 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Se asigna al controlador `Home` y al método `Index` (`id` se omite).        |

El uso de una plantilla suele ser el método de enrutamiento más sencillo. Las restricciones y los valores predeterminados también se pueden especificar fuera de la plantilla de ruta.

> [!TIP]
> Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.

## <a name="route-constraint-reference"></a>Referencia de restricción de ruta

Las restricciones de ruta se ejecutan cuando se ha producido una coincidencia con la dirección URL entrante y la ruta de dirección URL se convierte en tokens en valores de ruta. En general, las restricciones de ruta inspeccionan el valor de ruta asociado a través de la plantilla de ruta y deciden si el valor es aceptable o no. Algunas restricciones de ruta usan datos ajenos al valor de ruta para decidir si la solicitud se puede enrutar. Por ejemplo, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> puede aceptar o rechazar una solicitud basada en su verbo HTTP. Las restricciones se usan en las solicitudes de enrutamiento y la generación de vínculos.

> [!WARNING]
> No use las restricciones para las **validación de entrada** . Si las restricciones se usan para la **validación de entrada** , los resultados de entrada no válidos producirán un error *404 - No encontrado* en lugar de un error *400 - Solicitud incorrecta* con un mensaje de error adecuado. Las restricciones de ruta se usan para **eliminar la ambigüedad** entre rutas similares, no para validar las entradas de una ruta determinada.

En la tabla siguiente se muestran algunas restricciones de ruta de ejemplo y su comportamiento esperado.

| restricción | Ejemplo | Coincidencias de ejemplo | Notas |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Coincide con cualquier entero |
| `bool` | `{active:bool}` | `true`, `FALSE` | Coincide con `true` o `false` (no distingue mayúsculas de minúsculas) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Coincide con un valor `DateTime` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Coincide con un valor `decimal` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Coincide con un valor `double` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Coincide con un valor `float` válido en la referencia cultural invariable. Vea la advertencia anterior.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Coincide con un valor `Guid` válido |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Coincide con un valor `long` válido |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La cadena debe tener al menos cuatro caracteres |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | La cadena no debe tener más de ocho caracteres |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La cadena debe tener una longitud de exactamente 12 caracteres |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La cadena debe tener una longitud como mínimo de ocho caracteres y como máximo de 16 |
| `min(value)` | `{age:min(18)}` | `19` | El valor entero debe ser como mínimo 18 |
| `max(value)` | `{age:max(120)}` | `91` | El valor entero debe ser como máximo 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | El valor entero debe ser como mínimo 18 y máximo 120 |
| `alpha` | `{name:alpha}` | `Rick` | La cadena debe constar de uno o más caracteres alfabéticos (`a`-`z`, no distingue mayúsculas de minúsculas) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La cadena debe coincidir con la expresión regular (vea las sugerencias sobre cómo definir una expresión regular) |
| `required` | `{name:required}` | `Rick` | Se usa para exigir que un valor que no es de parámetro esté presente durante la generación de dirección URL |

Es posible aplicar varias restricciones delimitadas por dos puntos a un único parámetro. Por ejemplo, la siguiente restricción permite limitar un parámetro a un valor entero de 1 o superior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o `DateTime`) usan siempre la referencia cultural invariable. Estas restricciones dan por supuesto que la dirección URL no es localizable. Las restricciones de ruta proporcionadas por el marco de trabajo no modifican los valores almacenados en los valores de ruta. Todos los valores de ruta analizados desde la dirección URL se almacenan como cadenas. Por ejemplo, la restricción `float` intenta convertir el valor de ruta en un valor Float, pero el valor convertido se usa exclusivamente para comprobar que se puede convertir en Float.

## <a name="regular-expressions"></a>Expresiones regulares

El marco de trabajo de ASP.NET Core agrega `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` al constructor de expresiones regulares. Vea <xref:System.Text.RegularExpressions.RegexOptions> para obtener una descripción de estos miembros.

Las expresiones regulares usan delimitadores y tokens similares a los que usan el enrutamiento y el lenguaje C#. Es necesario usar secuencias de escape con los tokens de expresiones regulares. Para usar la expresión regular `^\d{3}-\d{2}-\d{4}$` en el enrutamiento, los caracteres `\` (una barra diagonal inversa) de la cadena se deben proporcionar como caracteres `\\` (barra diagonal inversa doble) en el archivo de código fuente de C# para que el carácter de escape de cadena `\` tenga una secuencia de escape (a menos que se usen [literales de cadena textuales](/dotnet/csharp/language-reference/keywords/string)). Para aplicar secuencias de escape a los caracteres delimitadores de parámetro de enrutamiento (`{`, `}`, `[` y `]`), duplique los caracteres en la expresión (`{{`, `}`, `[[` y `]]`). En la tabla siguiente se muestra una expresión regular y la versión con la secuencia de escape.

| Expresión regular    | Expresión regular con secuencia de escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Las expresiones regulares que se usan en el enrutamiento suelen empezar con el carácter de acento circunflejo (`^`) y coinciden con la posición inicial de la cadena. Las expresiones suelen terminar con el carácter del signo de dólar (`$`) y coincidir con el final de la cadena. Los caracteres `^` y `$` garantizan que la expresión regular coincide con el valor completo del parámetro de ruta. Sin los caracteres `^` y `$`, la expresión regular coincide con cualquier subcadena de la cadena, algo que normalmente no es deseable. En la tabla siguiente se proporcionan ejemplos y se explica por qué coinciden o no.

| Expresión   | String    | Coincidir con | Comentario               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | 123abc456 | Sí   | Coincidencias de subcadenas     |
| `[a-z]{2}`   | mz        | Sí   | Coincide con la expresión    |
| `[a-z]{2}`   | MZ        | Sí   | No distingue mayúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | No    | Vea `^` y `$` más arriba |
| `^[a-z]{2}$` | 123abc456 | No    | Vea `^` y `$` más arriba |

Para obtener más información sobre la sintaxis de expresiones regulares, vea [Expresiones regulares de .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir un parámetro a un conjunto conocido de valores posibles, use una expresión regular. Por ejemplo, `{action:regex(^(list|get|create)$)}` solo hace coincidir el valor de ruta `action` con `list`, `get` o `create`. Si se pasa al diccionario de restricciones, la cadena `^(list|get|create)$` es equivalente. Las restricciones que se pasan al diccionario de restricciones (no insertado en una plantilla) que no coinciden con una de las restricciones conocidas también se tratan como expresiones regulares.

## <a name="custom-route-constraints"></a>Restricciones de ruta personalizadas

Además de las restricciones de ruta integradas, se pueden crear restricciones de ruta personalizadas implementando la interfaz <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. La interfaz <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contiene un único método, `Match`, que devuelve `true` si se cumple la restricción, y `false` en caso contrario.

Para utilizar una restricción <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personalizada, el tipo de restricción de ruta debe registrarse con el parámetro <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de la aplicación en el contenedor de servicios de la aplicación. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> es un diccionario que asigna claves de restricciones de ruta a implementaciones de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> que validen esas restricciones. El parámetro <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de una aplicación puede actualizarse en `Startup.ConfigureServices` como parte de una llamada a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) o configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> directamente con `services.Configure<RouteOptions>`. Por ejemplo:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La restricción puede aplicarse a las rutas de la manera habitual, usando el nombre especificado al registrar el tipo de restricción. Por ejemplo:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Referencia de generación de direcciones URL

En el ejemplo siguiente se muestra cómo se genera un vínculo a una ruta, dado un diccionario de valores de ruta y un valor <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

El valor <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generado al final del ejemplo anterior es `/package/create/123`. El diccionario proporciona los valores de ruta `operation` e `id` de la plantilla "Ruta de paquete de seguimiento", `package/{operation}/{id}`. Para obtener más información, vea el código de ejemplo de la sección [Uso de software intermedio de enrutamiento](#use-routing-middleware) o la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

El segundo parámetro del constructor <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> es una colección de *valores de ambiente* . Los valores de ambiente son adecuados porque limitan el número de valores que el desarrollador debe especificar dentro de un contexto de solicitud. Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos. En la acción `About` de `HomeController` de una aplicación ASP.NET Core MVC, no es necesario especificar el valor de ruta de controlador para vincular a la acción `Index` (se usará el valor de ambiente `Home`).

Los valores de ambiente que no coincidan con un parámetro se omiten. También se omiten los valores de ambiente cuando un valor proporcionado de forma explícita invalida el valor de ambiente. La coincidencia se produce de izquierda a derecha en la dirección URL.

Los valores que se proporcionan de forma explícita pero que no coinciden con un segmento de la ruta se agregan a la cadena de consulta. En la tabla siguiente se muestra el resultado cuando se usa la plantilla de ruta `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Resultado                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si una ruta tiene un valor predeterminado que no se corresponde con un parámetro y ese valor se proporciona de forma explícita, debe coincidir con el valor predeterminado:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La generación de vínculos solo genera un vínculo para esta ruta si se proporcionan los valores coincidentes para `controller` y `action`.

## <a name="complex-segments"></a>Segmentos complejos

Los segmentos complejos (por ejemplo, `[Route("/x{token}y")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo. Consulte [este código](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) para obtener una explicación detallada de cómo se comparan los segmentos complejos. El [código de ejemplo](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) no se usa en ASP.NET Core, pero proporciona una buena explicación de los segmentos complejos.

::: moniker-end
