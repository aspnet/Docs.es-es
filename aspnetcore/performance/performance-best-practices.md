---
title: Procedimientos recomendados de ASP.NET Core rendimiento
author: mjrousos
description: Sugerencias para aumentar el rendimiento en ASP.NET Core aplicaciones y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: 01575ec87d2d346da7367523ca5e257d53de4983
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722623"
---
# <a name="aspnet-core-performance-best-practices"></a>Procedimientos recomendados de ASP.NET Core rendimiento

Por [Mike Rousos](https://github.com/mjrousos)

En este artículo se proporcionan instrucciones para las prácticas recomendadas de rendimiento con ASP.NET Core.

## <a name="cache-aggressively"></a>Caché agresiva

El almacenamiento en caché se describe en varias partes de este documento. Para obtener más información, vea <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Comprender las rutas de acceso de código activas

En este documento, una ruta de acceso de *Código activo* se define como una ruta de código a la que se llama con frecuencia y en la que se produce la mayor parte del tiempo de ejecución. Las rutas de acceso de código activo normalmente limitan el escalado horizontal y el rendimiento de la aplicación y se describen en varias partes de este documento.

## <a name="avoid-blocking-calls"></a>Evitar llamadas de bloqueo

ASP.NET Core aplicaciones deben diseñarse para procesar muchas solicitudes simultáneamente. Las API asincrónicas permiten que un pequeño grupo de subprocesos controle miles de solicitudes simultáneas sin esperar a que se bloqueen las llamadas. En lugar de esperar a que se complete una tarea sincrónica de ejecución prolongada, el subproceso puede funcionar en otra solicitud.

Un problema de rendimiento común en ASP.NET Core aplicaciones es el bloqueo de llamadas que podrían ser asincrónicas. Muchas llamadas de bloqueo sincrónicas conducen a la [colapso de grupos de subprocesos](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) y a tiempos de respuesta degradados.

**No**:

* Bloquee la ejecución asincrónica mediante una llamada a [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) o [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Adquirir bloqueos en rutas de acceso de código comunes. ASP.NET Core aplicaciones tienen más rendimiento cuando se diseña para ejecutar código en paralelo.
* Llame a [Task. Run](/dotnet/api/system.threading.tasks.task.run) y espere inmediatamente. ASP.NET Core ya ejecuta código de aplicación en subprocesos normales del grupo de subprocesos, por lo que llamar a Task. Run solo produce una programación de grupo de subprocesos innecesaria adicional. Aunque el código programado bloquee un subproceso, Task. Run no lo impide.

**Haga**lo siguiente:

* Establecer [rutas de acceso de código activas](#understand-hot-code-paths) de forma asincrónica.
* Llame a las API de acceso a datos, e/s y de ejecución prolongada de forma asincrónica si hay disponible una API asincrónica. **No** use [Task. Run](/dotnet/api/system.threading.tasks.task.run) para convertir una API sincrónica en asincrónica.
* Hacer que las acciones de controlador/ Razor Página sean asincrónicas. Toda la pila de llamadas es asincrónica para beneficiarse de los patrones [Async/Await](/dotnet/csharp/programming-guide/concepts/async/) .

Se puede usar un generador de perfiles, como [PerfView](https://github.com/Microsoft/perfview), para buscar subprocesos que se agregan con frecuencia al [grupo de subprocesos](/windows/desktop/procthread/thread-pools). El `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` evento indica que un subproceso se ha agregado al grupo de subprocesos. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>Devolver IEnumerable \<T> o IAsyncEnumerable\<T>

`IEnumerable<T>`Al devolver desde una acción, el serializador genera una iteración sincrónica de la colección. El resultado es el bloqueo de llamadas y una posibilidad de colapso del grupo de subprocesos. Para evitar la enumeración sincrónica, use `ToListAsync` antes de devolver el Enumerable.

A partir de ASP.NET Core 3,0, `IAsyncEnumerable<T>` se puede usar como alternativa a `IEnumerable<T>` que enumera de forma asincrónica. Para obtener más información, vea [tipos de valor devuelto de acción del controlador](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).

## <a name="minimize-large-object-allocations"></a>Minimizar las asignaciones de objetos grandes

El [recolector de elementos no utilizados de .net Core](/dotnet/standard/garbage-collection/) administra la asignación y liberación de memoria automáticamente en ASP.net Core aplicaciones. La recolección automática de elementos no utilizados normalmente significa que los desarrolladores no tienen que preocuparse de cómo o Cuándo se libera la memoria. No obstante, la limpieza de los objetos sin referencia supone tiempo de CPU, por lo que los desarrolladores deben minimizar la asignación de objetos en [rutas de acceso de código activas](#understand-hot-code-paths). La recolección de elementos no utilizados es especialmente costosa en objetos grandes (> 85 K bytes). Los objetos grandes se almacenan en el [montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap) y requieren una recolección completa de elementos no utilizados (generación 2) para la limpieza. A diferencia de las colecciones de generación 0 y generación 1, una colección de generación 2 requiere una suspensión temporal de la ejecución de la aplicación. La asignación y cancelación de asignación frecuentes de objetos grandes pueden provocar un rendimiento incoherente.

Recomendaciones:

* **Considere la** posibilidad de almacenar en caché objetos grandes que se usan con frecuencia. Almacenar en caché objetos grandes evita asignaciones costosas.
* **Realice** búferes de grupo mediante un [ArrayPool \<T> ](/dotnet/api/system.buffers.arraypool-1) para almacenar matrices de gran tamaño.
* **No** asigne muchos objetos grandes de corta duración en rutas de [acceso de código activas](#understand-hot-code-paths).

Los problemas de memoria, como el anterior, se pueden diagnosticar revisando las estadísticas de la recolección de elementos no utilizados (GC) en [PerfView](https://github.com/Microsoft/perfview) y examinando:

* Tiempo de pausa de recolección de elementos no utilizados.
* El porcentaje de tiempo del procesador se emplea en la recolección de elementos no utilizados.
* Número de recolecciones de elementos no utilizados de la generación 0, 1 y 2.

Para obtener más información, consulte recolección [de elementos no utilizados y rendimiento](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimizar el acceso a datos y la e/s

Las interacciones con un almacén de datos y otros servicios remotos suelen ser las partes más lentas de una aplicación ASP.NET Core. Leer y escribir datos de forma eficaz es fundamental para lograr un buen rendimiento.

Recomendaciones:

* **Llame a** todas las API de acceso a datos de forma asincrónica.
* **No** recupere más datos de los necesarios. Escribir consultas para devolver solo los datos necesarios para la solicitud HTTP actual.
* **Considere la** posibilidad de almacenar en caché los datos a los que se accede con frecuencia recuperados de una base de datos o servicio remoto si se aceptan ligeramente datos desactualizados. En función del escenario, use [MemoryCache](xref:performance/caching/memory) o [DistributedCache](xref:performance/caching/distributed). Para obtener más información, vea <xref:performance/caching/response>.
* **Minimice los** recorridos de ida y vuelta de red. El objetivo es recuperar los datos necesarios en una sola llamada en lugar de varias llamadas.
* **Use** [consultas sin seguimiento](/ef/core/querying/tracking#no-tracking-queries) en Entity Framework Core al obtener acceso a los datos con fines de solo lectura. EF Core puede devolver los resultados de las consultas sin seguimiento de forma más eficaz.
* **Filtre y** agregue consultas LINQ (con `.Where` `.Select` instrucciones, o `.Sum` , por ejemplo) para que la base de datos realice el filtrado.
* **Tenga en** cuenta que EF Core resuelve algunos operadores de consulta en el cliente, lo que puede provocar una ejecución de consulta ineficaz. Para obtener más información, vea [problemas de rendimiento de evaluación de cliente](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **No** use consultas de proyección en colecciones, lo que puede dar como resultado la ejecución de consultas SQL "N + 1". Para obtener más información, vea [optimización de subconsultas correlacionadas](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Consulte [EF alto rendimiento](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) para ver los enfoques que pueden mejorar el rendimiento en aplicaciones a gran escala:

* [Agrupación de DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Consultas compiladas de manera explícita](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Se recomienda medir el impacto de los enfoques de alto rendimiento anteriores antes de confirmar el código base. Es posible que la complejidad adicional de las consultas compiladas no justifique la mejora del rendimiento.

Los problemas de consulta se pueden detectar revisando el tiempo empleado en acceder a los datos con [Application Insights](/azure/application-insights/app-insights-overview) o con las herramientas de generación de perfiles. La mayoría de las bases de datos también hacen que las estadísticas estén disponibles en relación con las consultas ejecutadas con frecuencia.

## <a name="pool-http-connections-with-httpclientfactory"></a>Agrupar conexiones HTTP con HttpClientFactory

Aunque [HttpClient](/dotnet/api/system.net.http.httpclient) implementa la `IDisposable` interfaz, se ha diseñado para su reutilización. `HttpClient`Las instancias cerradas dejan los sockets abiertos en el `TIME_WAIT` Estado durante un breve período de tiempo. Si se usa con frecuencia una ruta de código que crea y desecha `HttpClient` objetos, la aplicación puede agotar los sockets disponibles. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) se presentó en ASP.net Core 2,1 como solución para este problema. Controla las conexiones HTTP de agrupación para optimizar el rendimiento y la confiabilidad.

Recomendaciones:

* **No** cree ni elimine `HttpClient` instancias directamente.
* **Use** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) para recuperar `HttpClient` las instancias. Para obtener más información, consulte el artículo sobre el [uso de HttpClientFactory para implementar solicitudes HTTP resistentes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Mantenga las rutas de acceso de código comunes con rapidez

Desea que todo el código sea rápido. Las rutas de acceso de código llamadas con frecuencia son las más críticas para optimizar. Se incluyen los siguientes:

* Los componentes de middleware en la canalización de procesamiento de solicitudes de la aplicación, especialmente el middleware se ejecutan al principio de la canalización. Estos componentes tienen un gran impacto en el rendimiento.
* Código que se ejecuta para cada solicitud o varias veces por solicitud. Por ejemplo, registro personalizado, controladores de autorización o inicialización de servicios transitorios.

Recomendaciones:

* **No** use componentes de middleware personalizados con tareas de ejecución prolongada.
* **Use herramientas** de generación de perfiles de rendimiento, como [Visual Studio herramientas de diagnóstico](/visualstudio/profiling/profiling-feature-tour) o [PerfView](https://github.com/Microsoft/perfview)), para identificar las rutas de [acceso de código activas](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Completar tareas de ejecución prolongada fuera de las solicitudes HTTP

La mayoría de las solicitudes a una aplicación ASP.NET Core pueden controlarse mediante un modelo de página o controlador que llama a los servicios necesarios y devuelve una respuesta HTTP. En el caso de algunas solicitudes que implican tareas de ejecución prolongada, es mejor hacer que todo el proceso de solicitud-respuesta sea asincrónico.

Recomendaciones:

* **No** espere a que se completen las tareas de ejecución prolongada como parte del procesamiento de solicitudes HTTP normales.
* **Considere la** posibilidad de controlar las solicitudes de ejecución prolongada con [servicios en segundo plano](xref:fundamentals/host/hosted-services) o fuera de proceso con una [función de Azure](/azure/azure-functions/). Completar el trabajo fuera de proceso es especialmente útil para las tareas de uso intensivo de la CPU.
* **Utilice opciones** de comunicación en tiempo real, como [SignalR](xref:signalr/introduction) , para comunicarse con los clientes de forma asincrónica.

## <a name="minify-client-assets"></a>Activos de cliente de minimizar

ASP.NET Core aplicaciones con servidores front-end complejos suelen servir muchos archivos de JavaScript, CSS o image. El rendimiento de las solicitudes de carga inicial se puede mejorar:

* Agrupación, que combina varios archivos en uno.
* Minificar, que reduce el tamaño de los archivos quitando el espacio en blanco y los comentarios.

Recomendaciones:

* **Use la** [compatibilidad integrada](xref:client-side/bundling-and-minification) de ASP.net Core para la agrupación y la minificar de los recursos de cliente.
* **Tenga en** cuenta otras herramientas de terceros, como [WebPack](https://webpack.js.org/), para la administración de activos de clientes complejos.

## <a name="compress-responses"></a>Comprimir respuestas

 Reducir el tamaño de la respuesta normalmente aumenta la capacidad de respuesta de una aplicación, a menudo drásticamente. Una manera de reducir los tamaños de carga es comprimir las respuestas de una aplicación. Para obtener más información, vea [compresión de respuesta](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Usar la versión más reciente del ASP.NET Core

Cada nueva versión de ASP.NET Core incluye mejoras de rendimiento. Las optimizaciones en .NET Core y ASP.NET Core significan que las versiones más recientes superan normalmente las versiones anteriores. Por ejemplo, .NET Core 2,1 agregó compatibilidad con las expresiones regulares compiladas y benefitted desde [span \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay). ASP.NET Core 2,2 agrega compatibilidad con HTTP/2. [ASP.NET Core 3,0 agrega muchas mejoras](xref:aspnetcore-3.0) que reducen el uso de memoria y mejoran el rendimiento. Si el rendimiento es una prioridad, considere la posibilidad de actualizar a la versión actual de ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimizar excepciones

Las excepciones deben ser poco frecuentes. Iniciar y detectar excepciones es lento con respecto a otros patrones de flujo de código. Por este motivo, las excepciones no se deben utilizar para controlar el flujo de programa normal.

Recomendaciones:

* **No** use las excepciones de inicio o detección como medio de flujo de programa normal, especialmente en las [rutas de acceso de código activas](#understand-hot-code-paths).
* **Incluya lógica** en la aplicación para detectar y controlar las condiciones que provocarían una excepción.
* Se **producen excepciones** Throw o catch para condiciones inusuales o inesperadas.

Las herramientas de diagnóstico de aplicaciones, como Application Insights, pueden ayudar a identificar excepciones comunes en una aplicación que pueden afectar al rendimiento.

## <a name="performance-and-reliability"></a>Rendimiento y confiabilidad

En las secciones siguientes se proporcionan sugerencias de rendimiento y soluciones y problemas de confiabilidad conocidos.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Evitar leer o escribir sincrónicos en el cuerpo HttpRequest/HttpResponse

Todas las e/s de ASP.NET Core son asincrónicas. Los servidores implementan la `Stream` interfaz, que tiene sobrecargas sincrónicas y asincrónicas. Los asincrónicos deben ser preferibles para evitar el bloqueo de subprocesos de grupo de subprocesos. Los subprocesos de bloqueo pueden provocar colapsos de grupos de subprocesos.

No **lo haga:** En el ejemplo siguiente se utiliza <xref:System.IO.StreamReader.ReadToEnd*> . Bloquea el subproceso actual para esperar el resultado. Este es un ejemplo de [sincronización a través de Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

En el código anterior, `Get` Lee sincrónicamente el cuerpo completo de la solicitud HTTP en la memoria. Si el cliente se está cargando lentamente, la aplicación está realizando la sincronización a través de Async. La aplicación se sincroniza a través de Async porque Kestrel **no admite lecturas** sincrónicas.

**Haga lo siguiente:** En el ejemplo siguiente se utiliza <xref:System.IO.StreamReader.ReadToEndAsync*> y no bloquea el subproceso mientras se lee.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

El código anterior Lee asincrónicamente el cuerpo completo de la solicitud HTTP en la memoria.

> [!WARNING]
> Si la solicitud es grande, leer el cuerpo completo de la solicitud HTTP en la memoria podría producir una condición de memoria insuficiente (OOM). OOM puede producir una denegación de servicio.  Para obtener más información, consulte [evitar la lectura de cuerpos de solicitud grandes o cuerpos de respuesta](#arlb) en la memoria de este documento.

**Haga lo siguiente:** El ejemplo siguiente es totalmente asincrónico mediante un cuerpo de solicitud no almacenado en búfer:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

El código anterior deserializa de forma asincrónica el cuerpo de la solicitud en un objeto de C#.

## <a name="prefer-readformasync-over-requestform"></a>Preferir ReadFormAsync sobre solicitud. formulario

Use `HttpContext.Request.ReadFormAsync` en lugar de `HttpContext.Request.Form`.
`HttpContext.Request.Form` solo se puede leer de forma segura con las siguientes condiciones:

* El formulario se ha leído mediante una llamada a `ReadFormAsync` , y
* El valor del formulario almacenado en caché se está leyendo usando `HttpContext.Request.Form`

No **lo haga:** En el ejemplo siguiente se utiliza `HttpContext.Request.Form` .  `HttpContext.Request.Form` usa la [sincronización a través de Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) y puede conducir a la colapso del grupo de subprocesos.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Haga lo siguiente:** En el ejemplo siguiente `HttpContext.Request.ReadFormAsync` se usa para leer el cuerpo del formulario de forma asincrónica.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Evite leer cuerpos de solicitud o cuerpos de respuesta de gran tamaño en la memoria

En .NET, cada asignación de objetos superior a 85 KB termina en el montón de objetos grandes ([montón](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Los objetos grandes son caros de dos maneras:

* El costo de asignación es alto porque la memoria de un objeto grande recién asignado tiene que borrarse. CLR garantiza que se borra la memoria para todos los objetos recién asignados.
* El montón se recopila con el resto del montón. El montón de elementos requiere una recolección completa de [elementos no utilizados](/dotnet/standard/garbage-collection/fundamentals) o una [colección](/dotnet/standard/garbage-collection/fundamentals#generations)de la segunda.

En esta [entrada de blog](https://adamsitnik.com/Array-Pool/#the-problem) se describe el problema brevemente:

> Cuando se asigna un objeto grande, se marca como objeto de gen. 2. No gen 0 como para objetos pequeños. Las consecuencias son que, si se queda sin memoria en el montón de información, el GC limpia todo el montón administrado, no solo un montón de montón. De este modo, limpia la generación 0, la generación 1 y la generación 2, incluido el montón de negocio. Esto se conoce como recolección de elementos no utilizados completa y es la recolección de elementos no utilizados más prolongada. Para muchas aplicaciones, puede ser aceptable. Pero definitivamente no para servidores Web de alto rendimiento, donde se necesitan pocos búferes de gran tamaño para controlar una solicitud Web media (leer de un socket, descomprimir, descodificar JSON & más).

Almacenar de un cuerpo de solicitud o de respuesta de gran tamaño en un único `byte[]` o `string` :

* Puede dar lugar a un espacio insuficiente en el montón.
* Puede producir problemas de rendimiento de la aplicación debido a la ejecución de GC completos.

## <a name="working-with-a-synchronous-data-processing-api"></a>Trabajar con una API de procesamiento de datos sincrónicos

Cuando se usa un serializador/deserializador que solo admite lecturas y escrituras sincrónicas (por ejemplo,  [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Almacene los datos en memoria de forma asincrónica antes de pasarlos al serializador/deserializador.

> [!WARNING]
> Si la solicitud es grande, puede dar lugar a una condición de memoria insuficiente (OOM). OOM puede producir una denegación de servicio.  Para obtener más información, consulte [evitar la lectura de cuerpos de solicitud grandes o cuerpos de respuesta](#arlb) en la memoria de este documento.

ASP.NET Core 3,0 utiliza <xref:System.Text.Json> de forma predeterminada para la serialización de JSON. <xref:System.Text.Json>:

* Lee y escribe JSON de forma asincrónica.
* Está optimizado para texto UTF-8.
* Normalmente, mayor rendimiento que `Newtonsoft.Json`.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>No almacenar IHttpContextAccessor. HttpContext en un campo

[IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) devuelve el `HttpContext` de la solicitud activa cuando se tiene acceso a él desde el subproceso de solicitud. `IHttpContextAccessor.HttpContext` **No** debe almacenarse en un campo o variable.

No **lo haga:** En el ejemplo siguiente `HttpContext` se almacena en un campo y, a continuación, se intenta usarlo más adelante.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Con frecuencia, el código anterior captura un valor null o incorrecto `HttpContext` en el constructor.

**Haga lo siguiente:** En el ejemplo siguiente:

* Almacena <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> en un campo.
* Utiliza el `HttpContext` campo en el momento correcto y comprueba `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>No tener acceso a HttpContext desde varios subprocesos

`HttpContext`*no* es seguro para subprocesos. El acceso `HttpContext` desde varios subprocesos en paralelo puede dar lugar a un comportamiento indefinido, como bloqueos, bloqueos y daños en los datos.

No **lo haga:** En el ejemplo siguiente se realizan tres solicitudes paralelas y se registra la ruta de acceso de la solicitud entrante antes y después de la solicitud HTTP de salida. Se tiene acceso a la ruta de acceso de la solicitud desde varios subprocesos, potencialmente en paralelo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Haga lo siguiente:** En el ejemplo siguiente se copian todos los datos de la solicitud entrante antes de efectuar las tres solicitudes paralelas.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>No usar HttpContext una vez completada la solicitud

`HttpContext` solo es válido mientras haya una solicitud HTTP activa en la canalización ASP.NET Core. Toda la canalización de ASP.NET Core es una cadena asincrónica de delegados que ejecuta cada solicitud. Cuando `Task` se completa la devuelta desde esta cadena, `HttpContext` se recicla.

No **lo haga:** En el ejemplo siguiente `async void` se utiliza la solicitud HTTP completada cuando `await` se alcanza la primera:

* Que **siempre** es una práctica incorrecta en ASP.net Core aplicaciones.
* Obtiene acceso a `HttpResponse` una vez completada la solicitud HTTP.
* Bloquea el proceso.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Haga lo siguiente:** En el ejemplo siguiente se devuelve una `Task` al marco de trabajo, por lo que la solicitud HTTP no se completa hasta que se completa la acción.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>No capturar el HttpContext en subprocesos en segundo plano

No **lo haga:** En el ejemplo siguiente se muestra un cierre que captura el `HttpContext` de la `Controller` propiedad. Esta es una práctica incorrecta porque el elemento de trabajo podría:

* Ejecutarse fuera del ámbito de la solicitud.
* Intento de leer el error `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Haga lo siguiente:** En el ejemplo siguiente:

* Copia los datos necesarios en la tarea en segundo plano durante la solicitud.
* No hace referencia a nada del controlador.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Las tareas en segundo plano se deben implementar como servicios hospedados. Para más información, consulte [Tareas en segundo plano con servicios hospedados](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>No capturar servicios insertados en los controladores de subprocesos en segundo plano

No **lo haga:** En el ejemplo siguiente se muestra un cierre que captura `DbContext` desde el `Controller` parámetro action. Esta es una práctica incorrecta.  El elemento de trabajo se puede ejecutar fuera del ámbito de la solicitud. `ContosoDbContext`Está en el ámbito de la solicitud, lo que produce una `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Haga lo siguiente:** En el ejemplo siguiente:

* Inserta un <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> objeto para crear un ámbito en el elemento de trabajo en segundo plano. `IServiceScopeFactory` es un singleton.
* Crea un nuevo ámbito de inserción de dependencias en el subproceso en segundo plano.
* No hace referencia a nada del controlador.
* No captura el `ContosoDbContext` de la solicitud entrante.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

El siguiente código resaltado:

* Crea un ámbito para la duración de la operación en segundo plano y resuelve los servicios de la misma.
* Usa `ContosoDbContext` desde el ámbito correcto.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>No modifique el código de estado ni los encabezados una vez iniciado el cuerpo de la respuesta.

ASP.NET Core no almacena en búfer el cuerpo de la respuesta HTTP. La primera vez que se escribe la respuesta:

* Los encabezados se envían junto con ese fragmento del cuerpo al cliente.
* Ya no es posible cambiar los encabezados de respuesta.

No **lo haga:** El código siguiente intenta agregar encabezados de respuesta una vez que ya se ha iniciado la respuesta:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

En el código anterior, `context.Response.Headers["test"] = "test value";` producirá una excepción si `next()` se ha escrito en la respuesta.

**Haga lo siguiente:** En el ejemplo siguiente se comprueba si la respuesta HTTP se ha iniciado antes de modificar los encabezados.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Haga lo siguiente:** En el ejemplo siguiente `HttpResponse.OnStarting` se usa para establecer los encabezados antes de que los encabezados de respuesta se vacíen en el cliente.

La comprobación de si la respuesta no se ha iniciado permite registrar una devolución de llamada que se invocará justo antes de que se escriban los encabezados de respuesta. Comprobando si la respuesta no se ha iniciado:

* Proporciona la capacidad de anexar o invalidar los encabezados Just-in-Time.
* No es necesario conocer el siguiente middleware de la canalización.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>No llame a Next () si ya ha empezado a escribir en el cuerpo de la respuesta.

Los componentes solo esperan ser llamados si es posible que puedan controlar y manipular la respuesta.

## <a name="use-in-process-hosting-with-iis"></a>Usar el hospedaje en proceso con IIS

Con el hospedaje en proceso, una aplicación ASP.NET Core se ejecuta en el mismo proceso que su proceso de trabajo de IIS. El hospedaje en proceso proporciona un rendimiento mejorado en el hospedaje fuera de proceso, ya que las solicitudes no se procesan en proxy a través del adaptador de bucle invertido. El adaptador de bucle invertido es una interfaz de red que devuelve el tráfico de red saliente al mismo equipo. IIS controla la administración de procesos con el [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Los proyectos tienen como valor predeterminado el modelo de hospedaje en proceso en ASP.NET Core 3,0 y versiones posteriores.

Para obtener más información, consulte [Host ASP.net Core en Windows con IIS](xref:host-and-deploy/iis/index) .