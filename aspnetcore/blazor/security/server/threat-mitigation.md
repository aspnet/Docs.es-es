---
title: Guía de mitigación de amenazas para ASP.NET Core Blazor Server
author: guardrex
description: Obtenga información sobre cómo mitigar las amenazas de seguridad para las aplicaciones Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/05/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 2637645da9db7d52668c6a36c822df25520ff1f1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013819"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a>Guía de mitigación de amenazas para ASP.NET Core Blazor Server

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Las aplicaciones Blazor Server adoptan un modelo de procesamiento de datos *con estado*, donde el servidor y el cliente mantienen una relación de larga duración. El estado persistente se mantiene mediante un [circuito](xref:blazor/state-management), el cual puede abarcar conexiones que también son potencialmente de larga duración.

Cuando un usuario visita un sitio web de Blazor Server, el servidor crea un circuito en su memoria. Este circuito indica al explorador qué contenido se va a representar y responde a los eventos, como cuando el usuario selecciona un botón en la UI. Para realizar estas acciones, el circuito invoca funciones de JavaScript en el explorador del usuario y métodos de .NET en el servidor. Esta interacción bidireccional basada en JavaScript se conoce como [interoperabilidad de JavaScript (interoperabilidad de JS)](xref:blazor/call-javascript-from-dotnet).

Dado que la interoperabilidad de JS se produce a través de Internet y el cliente usa un explorador remoto, las aplicaciones Blazor Server comparten la mayoría de los problemas de seguridad de las aplicaciones web. En este tema se describen las amenazas más habituales para las aplicaciones Blazor Server y se proporciona una guía de mitigación de amenazas centrada en las aplicaciones accesibles desde Internet.

En entornos restringidos, como redes o intranets corporativas, algunas de las instrucciones de mitigación:

* No se aplican en el entorno restringido.
* No merecen la pena porque el riesgo de seguridad en los entornos restringidos es bajo.

## <a name="no-locblazor-and-shared-state"></a>Blazor y estado compartido

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Agotamiento de recursos

El agotamiento de recursos puede producirse cuando un cliente interactúa con el servidor y hace que este consuma demasiados recursos. El consumo excesivo de recursos afecta principalmente a los siguientes elementos:

* [CPU](#cpu)
* [Memoria](#memory)
* [Conexiones de cliente](#client-connections)

Los ataques por denegación de servicio (DoS) suelen intentar agotar los recursos de una aplicación o de un servidor. Pero el agotamiento de recursos no tiene por qué deberse a un ataque en el sistema. Por ejemplo, una cantidad limitada de recursos se puede agotar debido a una alta demanda de los usuarios. Los ataques DoS se describen con más detalle en la sección [Ataques por denegación de servicio (DoS)](#denial-of-service-dos-attacks).

Los recursos externos al marco de Blazor, como las bases de datos y los identificadores de archivo (que se usan para leer y escribir archivos), también pueden experimentar el agotamiento de recursos. Para obtener más información, vea <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

El agotamiento de la CPU puede producirse cuando uno o varios clientes fuerzan al servidor a realizar trabajos que consumen mucha CPU.

Por ejemplo, imagine una aplicación Blazor Server que calcula un *número de Fibonacci*. Un número de Fibonacci se genera a partir de una secuencia de Fibonacci, en la que cada número es la suma de los dos anteriores. La cantidad de trabajo necesaria para obtener una respuesta depende de la longitud de la secuencia y del tamaño del valor inicial. Si la aplicación no establece límites en la solicitud de un cliente, los cálculos que requieren un uso intensivo de la CPU pueden acaparar el tiempo de la CPU y reducir el rendimiento de otras tareas. Un consumo excesivo de recursos es un problema de seguridad que afecta a la disponibilidad.

El agotamiento de la CPU supone un problema para todas las aplicaciones de acceso público. En las aplicaciones web normales, las solicitudes y las conexiones agotan el tiempo de espera como medida de seguridad, pero las aplicaciones Blazor Server no proporcionan las mismas medidas de seguridad. Las aplicaciones Blazor Server deben incluir las comprobaciones y los límites pertinentes para poder llevar a cabo un trabajo que requiera un uso potencialmente intensivo de la CPU.

### <a name="memory"></a>Memoria

El agotamiento de la memoria puede producirse cuando uno o varios clientes fuerzan al servidor a consumir una gran cantidad de memoria.

Por ejemplo, considere una aplicación Blazor del lado servidor con un componente que acepta y muestra una lista de elementos. Si la aplicación Blazor no impone límites en el número de elementos permitidos o en el número de elementos representados en el cliente, el procesamiento y la representación que necesitan mucha memoria pueden dominar la memoria del servidor hasta el punto en el que el rendimiento del servidor puede verse afectado. El servidor puede bloquearse o ralentizarse hasta el punto de parecer que se ha bloqueado.

Tenga en cuenta lo siguiente a la hora de mantener y mostrar una lista de los elementos que pertenecen a un posible escenario de agotamiento de la memoria en el servidor:

* Los elementos de una propiedad o de un campo `List<MyItem>` usan la memoria del servidor. Si la aplicación permite que la lista de elementos crezca sin límites, existe el riesgo de que el servidor se quede sin memoria. Al quedarse sin memoria, la sesión actual finaliza (se bloquea) y todas las sesiones simultáneas en esa instancia de servidor reciben una excepción de memoria insuficiente. Para evitar que se produzca este escenario, la aplicación debe usar una estructura de datos que imponga un límite de elementos a los usuarios simultáneos.
* Si no se usa un esquema de paginación para la representación, el servidor consume memoria adicional para los objetos que no están visibles en la interfaz de usuario. Sin un límite en cuanto al número de elementos, las demandas de memoria pueden agotar la memoria disponible del servidor. Para impedir este escenario, siga uno de estos procedimientos:
  * Use listas paginadas para la representación.
  * Muestre solo los primeros elementos (entre 100 y 1000) y exija al usuario que escriba criterios de búsqueda para ver más elementos de los mostrados.
  * Para un escenario de representación más avanzado, implemente listas o cuadrículas que admitan la *virtualización*. Con la virtualización, las listas solo representan un subconjunto de elementos actualmente visibles para el usuario. Cuando el usuario interactúa con la barra de desplazamiento en la UI, el componente solo representa los elementos que es necesario mostrar. Los elementos que no es necesario mostrar en ese momento se pueden conservar en el almacenamiento secundario; este es el enfoque ideal. Los elementos no mostrados también se pueden almacenar en la memoria, lo que resulta menos idóneo.

Las aplicaciones Blazor Server ofrecen un modelo de programación similar a otros marcos de UI para las aplicaciones con estado, como WPF, Windows Forms o Blazor WebAssembly. La principal diferencia es que, en varios marcos de interfaz de usuario, la memoria consumida por la aplicación pertenece al cliente y solo afecta a ese cliente en concreto. Por ejemplo, una aplicación Blazor WebAssembly se ejecuta completamente en el cliente y solo usa recursos de la memoria del cliente. En el escenario de Blazor Server, la memoria consumida por la aplicación pertenece al servidor y se comparte entre todos los clientes en la instancia del servidor.

Las demandas de memoria del lado servidor se deben tener en cuenta para todas las aplicaciones Blazor Server. Sin embargo, la mayoría de las aplicaciones web no tienen estado y la memoria que se usa al procesar una solicitud se libera en cuanto se devuelve una respuesta. Como recomendación general, no permita que los clientes asignen una cantidad de memoria sin límite, como en cualquier otra aplicación del lado servidor que conserve las conexiones de cliente. La memoria que consume una aplicación Blazor Server persiste durante más tiempo que una única solicitud.

> [!NOTE]
> Durante el desarrollo, se puede usar un generador de perfiles o un seguimiento capturado para evaluar las demandas de memoria de los clientes. Un generador de perfiles o un seguimiento no capturan la memoria asignada a un cliente en concreto. Para capturar el uso de memoria de un cliente específico durante el desarrollo, capture un volcado de memoria y examine la demanda de memoria de todos los objetos cuya raíz se encuentre en el circuito de ese usuario.

### <a name="client-connections"></a>Conexiones de cliente

Puede producirse un agotamiento de las conexiones si uno o varios clientes abren demasiadas conexiones simultáneas al servidor, lo que impide que otros clientes establezcan nuevas conexiones.

Los clientes de Blazor establecen una conexión única por sesión y la mantienen abierta mientras la ventana del explorador está abierta. La demanda de mantenimiento de todas las conexiones en el servidor no es específica de las aplicaciones Blazor. Dada la naturaleza persistente de las conexiones y la naturaleza con estado de las aplicaciones Blazor Server, el agotamiento de las conexiones supone un riesgo mayor para la disponibilidad de este tipo de aplicaciones.

De forma predeterminada, no hay ningún límite en cuanto al número de conexiones disponibles por usuario para una aplicación Blazor Server. Si la aplicación requiere un límite de conexiones, siga uno de estos procedimientos:

* Requiera autenticación para limitar de forma natural la posibilidad de que usuarios no autorizados se conecten a la aplicación. Para que este escenario sea eficaz, se debe impedir que los usuarios puedan aprovisionar nuevos usuarios a voluntad.
* Limite el número de conexiones por usuario. La limitación de las conexiones se puede realizar de las siguientes maneras. Preste atención para permitir que los usuarios legítimos tengan acceso a la aplicación (por ejemplo, cuando se establece un límite de conexiones en función de la dirección IP del cliente).
  * En el nivel de aplicación:
    * Implemente la extensibilidad de enrutamiento de puntos de conexión.
    * Requiera autenticación para conectarse a la aplicación y realice un seguimiento de las sesiones activas por usuario.
    * Rechace nuevas sesiones cuando se alcance un límite.
    * Redirija las conexiones WebSocket a una aplicación mediante un proxy, como [Azure SignalR Service](/azure/azure-signalr/signalr-overview), que multiplexa las conexiones de los clientes a una aplicación. Esto proporciona una aplicación con mayor capacidad de conexión de la que puede establecer un solo cliente, lo que impide que un único cliente agote las conexiones al servidor.
  * En el nivel de servidor: Use un proxy o una puerta de enlace delante de la aplicación. Por ejemplo, [Azure Front Door](/azure/frontdoor/front-door-overview) le permite definir, administrar y supervisar el enrutamiento global del tráfico web a una aplicación.

## <a name="denial-of-service-dos-attacks"></a>Ataques por denegación de servicio (DoS)

Los ataques por denegación de servicio (DoS) se producen cuando un cliente provoca que el servidor agote uno o más de sus recursos, lo que hace que la aplicación no esté disponible. Las aplicaciones Blazor Server incluyen algunos límites predeterminados y dependen de otros límites de ASP.NET Core y SignalR para ofrecer protección contra los ataques DoS que se establecen en <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>.

| Límite de aplicaciones Blazor Server | Descripción | Default |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Número máximo de circuitos desconectados que contiene a la vez un servidor determinado en la memoria. | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Cantidad máxima de tiempo que un circuito desconectado se conserva en la memoria antes de desactivarlo. | 3 minutos |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Cantidad máxima de tiempo que el servidor aguarda antes de agotar el tiempo de espera de invocación de una función de JavaScript asincrónica. | 1 minuto |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | Número máximo de lotes de representación no confirmados por circuito que el servidor mantiene en la memoria en un momento dado para admitir una reconexión sólida. Después de alcanzar el límite, el servidor deja de generar nuevos lotes de representación hasta que el cliente confirma uno o varios lotes. | 10 |

Establezca el tamaño máximo del mensaje para un solo mensaje entrante del centro de conectividad con <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions>.

| Límite de SignalR y ASP.NET Core | Descripción | Default |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Tamaño del mensaje para un mensaje individual. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interacciones con el explorador (cliente)

Un cliente interactúa con el servidor a través del envío de eventos de interoperabilidad de JS y la finalización de la representación. La comunicación de interoperabilidad de JS es bidireccional entre JavaScript y .NET:

* Los eventos del explorador se envían desde el cliente al servidor de manera asincrónica.
* El servidor responde de forma asincrónica mediante la representación de la interfaz de usuario según sea necesario.

### <a name="javascript-functions-invoked-from-net"></a>Funciones de JavaScript invocadas desde .NET

En las llamadas a funciones de JavaScript desde métodos de .NET:

* Todas las invocaciones tienen un tiempo de espera configurable después del cual se produce un error y se devuelve una excepción <xref:System.OperationCanceledException> al autor de la llamada.
  * Hay un tiempo de espera predeterminado de un minuto para las llamadas (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>). Para configurar este límite, consulte <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Se puede proporcionar un token de cancelación para controlar la cancelación en cada llamada. Confíe en el tiempo de espera predeterminado de cada llamada, siempre que sea posible, y limite el tiempo de una llamada al cliente si se proporciona un token de cancelación.
* No se puede confiar en el resultado de una llamada de JavaScript. El cliente de la aplicación Blazor que se está ejecutando en el explorador busca la función de JavaScript que se va a invocar. Se invoca la función y se obtiene el resultado o se produce un error. Un cliente malintencionado podría intentar lo siguiente:
  * Provocar un problema en la aplicación devolviendo un error de la función de JavaScript.
  * Inducir un comportamiento no deseado en el servidor devolviendo un resultado inesperado de la función de JavaScript.

Tome las siguientes precauciones para protegerse frente a los escenarios anteriores:

* Encapsule las llamadas de interoperabilidad de JS dentro de instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) para tener en cuenta los errores que pueden producirse durante las invocaciones. Para obtener más información, vea <xref:blazor/fundamentals/handle-errors#javascript-interop>.
* Valide los datos devueltos por las invocaciones de interoperabilidad de JS, incluidos los mensajes de error, antes de realizar cualquier acción.

### <a name="net-methods-invoked-from-the-browser"></a>Métodos de .NET invocados desde el explorador

No confíe en las llamadas de JavaScript a métodos de .NET. Cuando un método de .NET se exponga a JavaScript, preste atención a cómo se invoca al método de .NET:

* Trate cualquier método de .NET expuesto a JavaScript como haría con un punto de conexión público a la aplicación.
  * Valide la entrada.
    * Asegúrese de que los valores se encuentran dentro de los intervalos esperados.
    * Asegúrese de que el usuario tiene permiso para realizar la acción solicitada.
  * No asigne una cantidad excesiva de recursos como parte de la invocación al método de .NET. Por ejemplo, realice comprobaciones y establezca límites en el uso de la CPU y la memoria.
  * Tenga en cuenta que los métodos estáticos y de instancia se pueden exponer a clientes de JavaScript. Evite compartir el estado entre las sesiones a menos que el diseño requiera su uso compartido con las restricciones pertinentes.
    * Para los métodos de instancia expuestos a través de objetos `DotNetReference` que se crearon originalmente mediante la inserción de dependencias, los objetos se deben registrar como objetos de ámbito. Esto se aplica a cualquier servicio de inserción de dependencias que use la aplicación Blazor Server.
    * En el caso de los métodos estáticos, evite establecer un estado que no se pueda limitar al cliente a menos que la aplicación comparta explícitamente el estado por diseño entre todos los usuarios de una instancia de servidor.
  * Evite pasar datos proporcionados por el usuario en parámetros a llamadas de JavaScript. Si es absolutamente necesario pasar los datos en parámetros, asegúrese de que el código de JavaScript controla el paso de datos sin introducir vulnerabilidades frente a [ataques de scripts entre sitios (XSS)](#cross-site-scripting-xss). Por ejemplo, no escriba datos proporcionados por el usuario en Document Object Model (DOM) estableciendo la propiedad `innerHTML` de un elemento. Considere la posibilidad de usar la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para deshabilitar `eval` y otros elementos primitivos de JavaScript no seguros.
* Evite implementar la distribución personalizada de invocaciones de .NET sobre la implementación de distribución del marco. La exposición de métodos de .NET en el explorador es un escenario avanzado, no se recomienda para el desarrollo general de Blazor.

### <a name="events"></a>Events

Los eventos proporcionan un punto de entrada a una aplicación Blazor Server. Las mismas reglas que se usan para proteger los puntos de conexión de las aplicaciones web se aplican al control de eventos en las aplicaciones Blazor Server. Un cliente malintencionado puede enviar los datos que quiera como la carga de un evento.

Por ejemplo:

* Un evento de cambio para un elemento `<select>` podría enviar un valor que no está dentro de las opciones que la aplicación presenta al cliente.
* Un elemento `<input>` podría enviar datos de texto al servidor omitiendo la validación del lado cliente.

La aplicación debe validar los datos de los eventos que controla. En el marco Blazor, los [componentes de formularios](xref:blazor/forms-validation) llevan a cabo validaciones básicas. Si la aplicación usa componentes de formularios personalizados, se debe escribir código personalizado para validar los datos de los eventos según corresponda.

Los eventos de Blazor Server son asincrónicos, por lo que se pueden enviar varios eventos al servidor antes de que la aplicación tenga tiempo de reaccionar mediante la generación de una nueva representación. Esto tiene algunas implicaciones de seguridad que se deben tener en cuenta. La limitación de las acciones de los clientes en la aplicación debe realizarse dentro de los controladores de eventos y no debe depender del estado de visualización representado en ese momento.

Imagínese que tiene un componente de contador que debe permitir que un usuario incremente un contador un máximo de tres veces. El botón para incrementar el contador se basa condicionalmente en el valor de `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Un cliente puede enviar uno o varios eventos de incremento antes de que el marco genere una nueva representación de este componente. El resultado es que el usuario puede incrementar `count` *más de tres veces* porque la interfaz de usuario no elimina el botón lo bastante rápido. En el ejemplo siguiente se muestra la manera correcta de limitar a tres los incrementos de `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Al agregar la comprobación `if (count < 3) { ... }` en el controlador, la decisión de incrementar `count` se basa en el estado actual de la aplicación. La decisión no se basa en el estado de la interfaz de usuario, como era el caso en el ejemplo anterior, por lo que el estado podría estar temporalmente obsoleto.

### <a name="guard-against-multiple-dispatches"></a>Protección contra varios envíos

Si una devolución de llamada de evento invoca de forma asincrónica una operación de larga duración, como la recuperación de datos de una base de datos o de un servicio externos, considere la posibilidad de usar una restricción. La restricción puede impedir que el usuario ponga en cola varias operaciones mientras otra operación está en curso mediante comentarios visuales. El siguiente código de componente establece `isLoading` en `true` mientras `GetForecastAsync` obtiene los datos del servidor. Mientras `isLoading` es `true`, el botón está deshabilitado en la UI:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

El patrón de restricción que se muestra en el ejemplo anterior solo funciona si la operación en segundo plano se ejecuta de forma asincrónica con el patrón `async`-`await`.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Cancelación anticipada para evitar el uso tras la eliminación del componente

Además de usar una restricción como se describe en la sección [Protección contra varios envíos](#guard-against-multiple-dispatches), considere la posibilidad de usar un token de cancelación (<xref:System.Threading.CancellationToken>) para cancelar operaciones de ejecución prolongada una vez eliminado el componente. Este enfoque tiene la ventaja adicional de evitar el *uso tras la eliminación* de los componentes:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Evitación de eventos que generan grandes cantidades de datos

Algunos eventos DOM, como `oninput` o `onscroll`, pueden generar una gran cantidad de datos. Evite el uso de estos eventos en las aplicaciones Blazor Server.

## <a name="additional-security-guidance"></a>Instrucciones de seguridad adicionales

Las instrucciones para proteger las aplicaciones de ASP.NET Core se aplican también a las aplicaciones Blazor Server y se tratan en las siguientes secciones:

* [Registro y datos confidenciales](#logging-and-sensitive-data)
* [Protección de la información en tránsito con HTTPS](#protect-information-in-transit-with-https)
* [Scripts entre sitios (XSS)](#cross-site-scripting-xss)
* [Protección entre orígenes](#cross-origin-protection)
* [Secuestro de clic](#click-jacking)
* [Redireccionamientos abiertos](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registro y datos confidenciales

Las interacciones de interoperabilidad de JS entre el cliente y el servidor se registran en los registros del servidor con instancias de <xref:Microsoft.Extensions.Logging.ILogger>. Blazor evita el registro de información confidencial, como eventos reales o entradas y salidas de interoperabilidad de JS.

Cuando se produce un error en el servidor, el marco notifica al cliente y anula la sesión. De forma predeterminada, el cliente recibe un mensaje de error genérico que se puede consultar en las herramientas de desarrollo del explorador.

El error del lado cliente no incluye la pila de llamadas y no proporciona detalles sobre la causa del error, pero los registros del servidor sí contienen dicha información. La información de errores confidencial puede ponerse a disposición del cliente con fines de desarrollo mediante la habilitación de errores detallados.

Habilite los errores detallados en JavaScript con:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* Clave de configuración `DetailedErrors` establecida en `true`, la cual se puede establecer en el archivo de configuración de la aplicación (`appsettings.json`). La clave también se puede establecer mediante la variable de entorno `ASPNETCORE_DETAILEDERRORS` con un valor `true`.

> [!WARNING]
> La exposición de información de errores a los clientes en Internet es un riesgo de seguridad que debe evitarse siempre.

### <a name="protect-information-in-transit-with-https"></a>Protección de la información en tránsito con HTTPS

Blazor Server usa SignalR para la comunicación entre el cliente y el servidor. Normalmente, Blazor Server usa el transporte que negocia SignalR, que suele ser WebSockets.

Blazor Server no garantiza la integridad y confidencialidad de los datos enviados entre el servidor y el cliente. Use siempre HTTPS.

### <a name="cross-site-scripting-xss"></a>Scripts entre sitios (XSS)

Los ataques de scripts entre sitios (XSS) permiten que una entidad no autorizada ejecute una lógica arbitraria en el contexto del explorador. Una aplicación en peligro podría ejecutar código arbitrario en el cliente. Esta vulnerabilidad podría usarse para realizar una serie de acciones malintencionadas en el servidor:

* Enviar eventos falsos o no válidos al servidor.
* Enviar finalizaciones de representaciones no válidas o con errores.
* Evitar el envío de finalizaciones de representaciones.
* Enviar llamadas de interoperabilidad de JavaScript a .NET.
* Modificar la respuesta de las llamadas de interoperabilidad de .NET a JavaScript.
* Evitar el envío de .NET a los resultados de interoperabilidad de JS.

El marco de Blazor Server toma las medidas necesarias para protegerle frente a algunas de las amenazas anteriores:

* Detiene la generación de nuevas actualizaciones de la interfaz de usuario si el cliente no reconoce los lotes de representación. Se configura con <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.
* Agota el tiempo de espera de cualquier llamada de .NET a JavaScript pasado un minuto sin recibir una respuesta del cliente. Se configura con <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.
* Realiza la validación básica de todas las entradas procedentes del explorador durante la interoperabilidad de JS:
  * Las referencias de .NET son válidas y del tipo esperado por el método de .NET.
  * Los datos no tienen un formato incorrecto.
  * La carga cuenta con el número adecuado de argumentos para el método.
  * Los argumentos o el resultado se pueden deserializar correctamente antes de invocar el método.
* Realiza la validación básica en todas las entradas procedentes del explorador a partir de los eventos enviados:
  * El evento tiene un tipo válido.
  * Los datos para el evento se pueden deserializar.
  * Hay un controlador de eventos asociado al evento.

Además de las medidas de seguridad que implementa el marco, el desarrollador debe codificar la aplicación para protegerla frente a amenazas y debe realizar las siguientes acciones:

* Validar siempre los datos al controlar los eventos.
* Tomar las medidas adecuadas al recibir datos no válidos:
  * Omitir los datos y devolverlos. Esto permite que la aplicación siga procesando las solicitudes.
  * Iniciar una excepción si la aplicación determina que la entrada es ilegítima y no la ha podido generar un cliente legítimo. Al iniciar una excepción, se anula el circuito y finaliza la sesión.
* No confiar en el mensaje de error proporcionado por las finalizaciones de lotes de representación incluidas en los registros. El cliente proporciona el error y, por lo general, no se puede confiar en él, ya que es posible que esté en peligro.
* No confiar en la entrada de las llamadas de interoperabilidad de JS entre los métodos de .NET y JavaScript y viceversa.
* La aplicación se encarga de validar que el contenido de los argumentos y de los resultados es válido, incluso si los argumentos o los resultados están correctamente deserializados.

Para que exista una vulnerabilidad frente a ataques de scripts entre sitios (XSS), la aplicación debe incorporar los datos proporcionados por el usuario en la página representada. Los componentes de Blazor Server ejecutan un paso en tiempo de compilación en el que el marcado de un archivo `.razor` se transforma en una lógica de procedimientos de C#. En tiempo de ejecución, la lógica de C# crea un *árbol de representación* que describe los elementos, el texto y los componentes secundarios. Esto se aplica al DOM del explorador a través de una secuencia de instrucciones de JavaScript (o se serializa a HTML en el caso de realizar una representación previa):

* Los datos proporcionados por el usuario mediante la sintaxis normal de Razor (por ejemplo, `@someStringValue`) no exponen una vulnerabilidad frente a ataques de scripts entre sitios (XSS) porque la sintaxis de Razor se agrega al DOM a través de comandos que solo pueden escribir texto. Aunque el valor incluya marcado HTML, se muestra como texto estático. Al generar una representación previa, la salida está codificada en HTML, que también muestra el contenido como texto estático.
* No se permiten etiquetas de script y no se deben incluir en el árbol de representación de componentes de la aplicación. Si se incluye una etiqueta de script en el marcado de un componente, se genera un error en tiempo de compilación.
* Los autores de componentes pueden crear componentes en C# sin usar Razor. El autor del componente es responsable de usar las API correctas al emitir la salida. Por ejemplo, debe usar `builder.AddContent(0, someUserSuppliedString)` y *no* `builder.AddMarkupContent(0, someUserSuppliedString)`, ya que esta última podría crear una vulnerabilidad frente a ataques de scripts entre sitios (XSS).

Como parte de la protección contra ataques XSS, considere la posibilidad de implementar mitigaciones de XSS, como la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Para obtener más información, vea <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Protección entre orígenes

Los ataques entre orígenes se producen cuando un cliente de un origen diferente realiza una acción en el servidor. La acción malintencionada es normalmente una solicitud GET o un método POST de formulario (falsificación de solicitud entre sitios, CSRF), pero también puede ser la apertura de un WebSocket malintencionado. Las aplicaciones Blazor Server ofrecen [las mismas garantías que cualquier otra aplicación SignalR que use la oferta de protocolo del centro de conectividad](xref:signalr/security):

* Se puede acceder a las aplicaciones Blazor Server desde diferentes orígenes, a menos que se tomen medidas adicionales para evitarlo. Para deshabilitar el acceso entre orígenes, deshabilite el uso compartido de recursos entre orígenes (CORS) en el punto de conexión agregando el middleware de CORS a la canalización y el atributo <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> a los metadatos del punto de conexión de Blazor, o bien limite el conjunto de orígenes permitidos mediante la [configuración de SignalR para el uso compartido de recursos entre orígenes](xref:signalr/security#cross-origin-resource-sharing).
* Si CORS está habilitado, podría ser necesario tomar medidas adicionales para proteger la aplicación en función de la configuración de CORS. Si CORS está habilitado globalmente, se puede deshabilitar para el centro de conectividad de Blazor Server agregando los metadatos de <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> a los metadatos del punto de conexión después de llamar a <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en el generador de rutas del punto de conexión.

Para obtener más información, vea <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Secuestro de clic

El secuestro de clic implica la representación de un sitio como un `<iframe>` dentro de otro sitio desde un origen diferente para engañar al usuario y que realice acciones en el sitio que está bajo ataque.

Para proteger una aplicación frente a su representación dentro de un elemento `<iframe>`, use la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) y el encabezado `X-Frame-Options`. Para obtener más información, vea [Documentación web de MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Redireccionamientos abiertos

Cuando se inicia una sesión de aplicación Blazor Server, el servidor realiza la validación básica de las direcciones URL que se envían como parte del inicio de la sesión. El marco comprueba que la URL base es un elemento primario de la dirección URL actual antes de establecer el circuito. El marco no realiza ninguna comprobación adicional.

Cuando un usuario selecciona un vínculo en el cliente, la dirección URL del vínculo se envía al servidor, que determina la acción que se debe realizar. Por ejemplo, la aplicación puede realizar una navegación en el lado cliente o indicar al explorador que vaya a la nueva ubicación.

Los componentes también pueden desencadenar solicitudes de navegación programáticamente a través del uso de <xref:Microsoft.AspNetCore.Components.NavigationManager>. En estos casos, la aplicación puede realizar una navegación del lado cliente o indicar al explorador que vaya a la nueva ubicación.

Los componentes deben:

* Evitar el uso de datos proporcionados por el usuario como parte de los argumentos de la llamada de navegación.
* Validar los argumentos para asegurarse de que la aplicación permite el destino.

De lo contrario, un usuario malintencionado puede forzar al explorador para que vaya a un sitio controlado por un atacante. En este escenario, el atacante engaña a la aplicación para que use algunos datos proporcionados por el usuario como parte de la invocación del método <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.

Los siguientes consejos también se aplican cuando se representan vínculos como parte de la aplicación:

* Si es posible, use vínculos relativos.
* Compruebe que los destinos de los vínculos absolutos son válidos antes de incluirlos en una página.

Para obtener más información, vea <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Lista de comprobación de seguridad

La siguiente lista de consideraciones de seguridad no está completa:

* Valide los argumentos de eventos.
* Valide las entradas y los resultados de las llamadas de interoperabilidad de JS.
* Evite usar (o validar previamente) datos proporcionados por el usuario para las llamadas de .NET a interoperabilidad de JS.
* Impida que el cliente asigne una cantidad de memoria ilimitada para:
  * Datos dentro del componente.
  * Referencias `DotNetObject` devueltas al cliente.
* Restrinja los envíos múltiples.
* Cancele las operaciones de ejecución prolongada cuando se elimina el componente.
* Evite los eventos que generan grandes cantidades de datos.
* Evite usar datos proporcionados por el usuario como parte de las llamadas a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>, pero si es inevitable, valide en primer lugar los datos de direcciones URL proporcionados por el usuario cotejándolos con un conjunto de orígenes permitidos.
* No tome decisiones de autorización basadas en el estado de la interfaz de usuario, solo en el estado del componente.
* Considere la posibilidad de usar la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para protegerse frente a ataques XSS.
* Considere la posibilidad de usar CSP y [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para protegerse frente al secuestro de clic.
* Asegúrese de que la configuración de CORS sea adecuada al habilitar CORS, o bien deshabilite CORS de forma explícita para las aplicaciones Blazor.
* Realice las pruebas necesarias para asegurarse de que los límites del servidor para la aplicación Blazor proporcionan una experiencia de usuario aceptable sin niveles de riesgo inaceptables.
