---
title: Realización de solicitudes HTTP mediante IHttpClientFactory en ASP.NET Core
author: stevejgordon
description: Obtenga información sobre cómo usar la interfaz IHttpClientFactory para administrar instancias de HttpClient lógicas en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: fb9001c06228b4290ca1e0c7cfb6b1338f431cd6
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444119"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Realización de solicitudes HTTP mediante IHttpClientFactory en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://github.com/serpent5)

Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación. `IHttpClientFactory` ofrece las ventajas siguientes:

* Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas. Por ejemplo, se podría registrar un cliente *github* y configurarlo para acceder a [GitHub](https://github.com/). Se puede registrar un cliente predeterminado para el acceso general.
* Codifica el concepto de middleware de salida a través de la delegación de controladores en `HttpClient`. Proporciona extensiones para el middleware basado en Polly a fin de aprovechar los controladores de delegación en `HttpClient`.
* Administra la agrupación y la duración de las instancias de `HttpClientMessageHandler` subyacentes. La administración automática evita los problemas comunes de DNS (Sistema de nombres de dominio) que se producen al administrar la duración de `HttpClient` de forma manual.
* Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

En el código de ejemplo de la versión este tema se usa <xref:System.Text.Json> para deserializar el contenido JSON devuelto en las respuestas HTTP. Para obtener ejemplos en los que se usan `Json.NET` y `ReadAsAsync<T>`, utilice el selector de versión para seleccionar una versión 2.x de este tema.

## <a name="consumption-patterns"></a>Patrones de consumo

`IHttpClientFactory` se puede usar de varias formas en una aplicación:

* [Uso básico](#basic-usage)
* [Clientes con nombre](#named-clients)
* [Clientes con tipo](#typed-clients)
* [Clientes generados](#generated-clients)

El mejor enfoque depende de los requisitos de la aplicación.

### <a name="basic-usage"></a>Uso básico

`IHttpClientFactory` se puede registrar mediante una llamada a `AddHttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Se puede solicitar una instancia de `IHttpClientFactory` mediante la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). En el código siguiente se usa `IHttpClientFactory` para crear una instancia de `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

El uso de `IHttpClientFactory` como en el ejemplo anterior es una buena manera de refactorizar una aplicación existente. No tiene efecto alguno en la forma de usar `HttpClient`. En aquellos sitios de una aplicación existente en los que ya se hayan creado instancias de `HttpClient`, reemplace esas repeticiones por llamadas a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes con nombre

Los clientes con nombre son una buena opción cuando:

* La aplicación requiere muchos usos distintos de `HttpClient`.
* Muchas instancias `HttpClient` de tienen otra configuración.

La configuración de un objeto `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

En el código anterior, el cliente está configurado con:

* La dirección base. `https://api.github.com/`.
* Dos encabezados necesarios para trabajar con la API de GitHub.

#### <a name="createclient"></a>CreateClient

Cada vez que se llama a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:

* Se crea una instancia de `HttpClient`.
* Se llama a la acción de configuración.

Para crear un cliente con nombre, pase su nombre a `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

En el código anterior, no es necesario especificar un nombre de host en la solicitud. El código puede pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.

### <a name="typed-clients"></a>Clientes con tipo

Clientes con tipo:

* Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.
* Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.
* Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él. Por ejemplo, es posible usar un solo cliente con tipo:
  * Para un único punto de conexión de back-end.
  * Para encapsular toda la lógica relacionada con el punto de conexión.
* Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.

Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

En el código anterior:

* La configuración se mueve al cliente con tipo.
* El objeto `HttpClient` se expone como una propiedad pública.

Se pueden crear métodos específicos de la API que exponen la funcionalidad de `HttpClient`. Por ejemplo, el método `GetAspNetDocsIssues` encapsula el código para recuperar incidencias abiertas.

En el código siguiente se llama a <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> en `Startup.ConfigureServices` para registrar una clase de cliente con tipo:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

El cliente con tipo se registra como transitorio con inserción con dependencias, En el código anterior, `AddHttpClient` registra `GitHubService` como servicio transitorio. Este registro usa un Factory Method para:

1. Crea una instancia de `HttpClient`.
1. Cree una instancia de `GitHubService`, pasando la instancia de `HttpClient` a su constructor.

y se puede insertar y consumir directamente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

La configuración de un cliente con tipo se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient` se puede encapsular dentro de un cliente con tipo. En lugar de exponerlo como una propiedad, defina un método que llame a la instancia de `HttpClient` internamente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

En el código anterior, `HttpClient` se almacena en un campo privado. El acceso a `HttpClient` se realiza mediante el método `GetRepos` público.

### <a name="generated-clients"></a>Clientes generados

`IHttpClientFactory` se puede usar en combinación con bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit). Refit es una biblioteca de REST para .NET que convierte las API de REST en interfaces en vivo. Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.

Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Un cliente con tipo se puede agregar usando Refit para generar la implementación:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a>Realización de solicitudes POST, PUT y DELETE

En los ejemplos anteriores, todas las solicitudes HTTP usan el verbo HTTP de GET. `HttpClient` también admite otros verbos HTTP; por ejemplo:

* POST
* PUT
* SUPRIMIR
* PATCH

Para obtener una lista completa de los verbos HTTP admitidos, vea <xref:System.Net.Http.HttpMethod>.

En el ejemplo siguiente se muestra cómo hacer una solicitud POST HTTP:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

En el código anterior, el método `CreateItemAsync`:

* Serializa el parámetro `TodoItem` en JSON mediante `System.Text.Json`. Usa una instancia de <xref:System.Text.Json.JsonSerializerOptions> para configurar el proceso de serialización.
* Crea una instancia de <xref:System.Net.Http.StringContent> a fin de empaquetar el JSON serializado para enviarlo en el cuerpo de la solicitud de HTTP.
* Llama a <xref:System.Net.Http.HttpClient.PostAsync%2A> para enviar el contenido de JSON a la URL especificada. Se trata de una URL relativa que se agrega a [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).
* Llama a <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> para iniciar una excepción si el código de estado de la respuesta no indica que la operación se ha procesado correctamente.

`HttpClient` también admite otros tipos de contenido. Por ejemplo, <xref:System.Net.Http.MultipartContent> y <xref:System.Net.Http.StreamContent>. Para obtener una lista completa del contenido admitido, vea <xref:System.Net.Http.HttpContent>.

En el ejemplo siguiente se muestra una solicitud PUT HTTP:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

El código anterior es muy similar al ejemplo de POST. El método `SaveItemAsync` llama a <xref:System.Net.Http.HttpClient.PutAsync%2A> en lugar de `PostAsync`.

En el ejemplo siguiente se muestra una solicitud DELETE HTTP:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

En el código anterior, el método `DeleteItemAsync` llama a <xref:System.Net.Http.HttpClient.DeleteAsync%2A>. Debido a que las solicitudes DELETE HTTP normalmente no contienen ningún cuerpo, el método `DeleteAsync` no proporciona ninguna sobrecarga que acepte una instancia de `HttpContent`.

Para obtener más información sobre el uso de verbos HTTP diferentes con `HttpClient`, vea <xref:System.Net.Http.HttpClient>.

## <a name="outgoing-request-middleware"></a>Middleware de solicitud saliente

`HttpClient` tiene el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes. `IHttpClientFactory`:

* simplifica la definición de controladores que se aplicarán por cada cliente con nombre.
* Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente. Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida. Este patrón:

  * Es similar a la canalización de middleware de entrada de ASP.NET Core.
  * Proporciona un mecanismo para administrar los intereses transversales relacionados con las solicitudes HTTP, como:

    * el almacenamiento en caché
    * el control de errores
    * la serialización
    * el registro

Para crear un controlador de delegación:

* Derívelo de <xref:System.Net.Http.DelegatingHandler>.
* Reemplace <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Ejecute el código antes de pasar la solicitud al siguiente controlador de la canalización:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

El código anterior comprueba si el encabezado `X-API-KEY` está en la solicitud. Si falta `X-API-KEY`, se devuelve <xref:System.Net.HttpStatusCode.BadRequest>.

Se puede agregar más de un controlador a la configuración de una instancia de `HttpClient` con <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias. `IHttpClientFactory` crea un ámbito de inserción de dependencias independiente para cada controlador. Los controladores pueden depender de servicios de cualquier ámbito. Los servicios de los que dependen los controladores se eliminan cuando se elimina el controlador.

Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, pasando el tipo del controlador.

Se pueden registrar varios controladores en el orden en que deben ejecutarse. Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:

* Pase datos al controlador mediante [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acceder a la solicitud actual.
* Cree un objeto de almacenamiento <xref:System.Threading.AsyncLocal`1> personalizado para pasar los datos.

## <a name="use-polly-based-handlers"></a>Usar controladores basados en Polly

`IHttpClientFactory` se integra con la biblioteca de terceros [Polly](https://github.com/App-vNext/Polly). Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET. Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.

Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas. Las extensiones de Polly permiten agregar controladores basados en Polly a los clientes. Polly requiere el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).

### <a name="handle-transient-faults"></a>Control de errores transitorios

Los errores se suelen producir cuando las llamadas HTTP externas son transitorias. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite definir una directiva para controlar los errores transitorios. Las directivas configuradas con `AddTransientHttpErrorPolicy` controlan las respuestas siguientes:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` proporciona acceso a un objeto `PolicyBuilder` configurado para controlar los errores que representan un posible error transitorio:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

En el código anterior, se define una directiva `WaitAndRetryAsync`. Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.

### <a name="dynamically-select-policies"></a>Seleccionar directivas dinámicamente

Los métodos de extensión se proporcionan para agregar controladores basados en Polly, por ejemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>. La siguiente sobrecarga de `AddPolicyHandler` inspecciona la solicitud para decidir qué directiva se debe aplicar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos. En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Agregar varios controladores de Polly

Es común anidar las directivas de Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

En el ejemplo anterior:

* Se agregan dos controladores.
* El primer controlador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para agregar una directiva de reintentos. Las solicitudes con error se reintentan hasta tres veces.
* La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor. Las solicitudes externas adicionales se bloquean durante 30 segundos si se producen cinco intentos con error seguidos. Las directivas de interruptor tienen estado. Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.

### <a name="add-policies-from-the-polly-registry"></a>Agregar directivas desde el Registro de Polly

Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`.

En el código siguiente:

* Se agregan las directivas "regular" y "long".
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> agrega las directivas "regular" y "long" del registro.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Para más información sobre `IHttpClientFactory` y las integraciones de Polly, vea la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient y administración de la duración

Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`. Se crea un objeto <xref:System.Net.Http.HttpMessageHandler> por cada cliente con nombre. La fábrica administra la duración de las instancias de `HttpMessageHandler`.

`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos. Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.

Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes. Crear más controladores de los necesarios puede provocar retrasos en la conexión. Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede impedir que el controlador reaccione ante los cambios de DNS (Sistema de nombres de dominio).

La duración de controlador predeterminada es dos minutos. El valor predeterminado se puede reemplazar de forma individual en cada cliente con nombre:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

Normalmente, las instancias de `HttpClient` se pueden trata como objetos de .NET que **no** requieren eliminación. ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`.

Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`. Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas a IHttpClientFactory

El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:

* Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.
* Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.

Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.

- Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.
- Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.

Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.

- `SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`. Este uso compartido impide el agotamiento del socket.
- `SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten. El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto. En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:

 - Deshabilitar el control automático de las cookies
 - Evitar `IHttpClientFactory`

Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registro

Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes. Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados. El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.

La categoría de registro usada en cada cliente incluye el nombre del cliente. Un cliente denominado *MyNamedClient*, por ejemplo, registra mensajes con una categoría de "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler". Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud. En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud. En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.

El registro también se produce dentro de la canalización de controlador de la solicitud. En el ejemplo *MyNamedClient*, esos mensajes se registran con la categoría de registro "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler". En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que se envíe la solicitud. En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.

Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización. Esto puede incluir cambios en los encabezados de solicitud o en el código de estado de la respuesta.

La inclusión del nombre del cliente en la categoría de registro permite filtrar el registro para clientes con nombre específicos.

## <a name="configure-the-httpmessagehandler"></a>Configurar HttpMessageHandler

Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.

Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo. Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado. Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Uso de IHttpClientFactory en una aplicación de consola

En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

En el ejemplo siguiente:

* <xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).
* `MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`. `HttpClient` se utiliza para recuperar una página web.
* `Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware de propagación de encabezados

La propagación de encabezados es un middleware ASP.NET Core que se usa para propagar encabezados HTTP de la solicitud entrante a las solicitudes de cliente HTTP salientes. Para utilizar la propagación de encabezados:

* Haga referencia al paquete [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).
* Configure el middleware y `HttpClient` en `Startup`:

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* El cliente incluye los encabezados configurados en las solicitudes salientes:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Recursos adicionales

* [Uso de HttpClientFactory para implementar solicitudes HTTP resistentes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementación del patrón de interruptor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Procedimiento para serializar y deserializar JSON en .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) y [Steve Gordon](https://github.com/stevejgordon)

Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación. Esto reporta las siguientes ventajas:

* Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas. Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a [GitHub](https://github.com/). y, de igual modo, registrar otro cliente predeterminado para otros fines.
* Codifica el concepto de middleware saliente a través de controladores de delegación en `HttpClient` y proporciona extensiones para middleware basado en Polly para poder sacar partido de este.
* Administra la agrupación y duración de las instancias de `HttpClientMessageHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.
* Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Patrones de consumo

`IHttpClientFactory` se puede usar de varias formas en una aplicación:

* [Uso básico](#basic-usage)
* [Clientes con nombre](#named-clients)
* [Clientes con tipo](#typed-clients)
* [Clientes generados](#generated-clients)

Ninguno de ellos es rigurosamente superior a otro, sino que el mejor método dependerá de las restricciones particulares de la aplicación.

### <a name="basic-usage"></a>Uso básico

Se puede registrar un `IHttpClientFactory` llamando al método de extensión `AddHttpClient` en `IServiceCollection`, dentro del método `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Una vez registrado, el código puede aceptar una interfaz `IHttpClientFactory` en cualquier parte donde se puedan insertar servicios por medio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). `IHttpClientFactory` se puede usar para crear una instancia de `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Cuando `IHttpClientFactory` se usa de este modo, constituye una excelente manera de refactorizar una aplicación existente. No tiene efecto alguno en la forma en que `HttpClient` se usa. En aquellos sitios en los que ya se hayan creado instancias de `HttpClient`, reemplace esas apariciones por una llamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes con nombre

Si una aplicación necesita usar `HttpClient` de diversas maneras, cada una con una configuración diferente, una opción consiste en usar **clientes con nombre**. La configuración de un `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

En el código anterior, se llama a `AddHttpClient` usando el nombre *github*. Este cliente tiene aplicadas algunas configuraciones predeterminadas, a saber, la dirección base y dos encabezados necesarios para trabajar con la API de GitHub.

Cada vez que se llama a `CreateClient`, se crea otra instancia de `HttpClient` y se llama a la acción de configuración.

Para consumir un cliente con nombre, se puede pasar un parámetro de cadena a `CreateClient`. Especifique el nombre del cliente que se va a crear:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

En el código anterior, no es necesario especificar un nombre de host en la solicitud. Basta con pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.

### <a name="typed-clients"></a>Clientes con tipo

Clientes con tipo:

* Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.
* Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.
* Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él. Por ejemplo, el mismo cliente con tipo se puede usar para un punto de conexión back-end único y encapsular toda la lógica que se ocupa de ese punto de conexión.
* Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.

Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

En el código anterior, la configuración se mueve al cliente con tipo. El objeto `HttpClient` se expone como una propiedad pública. Se pueden definir métodos específicos de API que exponen la funcionalidad `HttpClient`. El método `GetAspNetDocsIssues` encapsula el código necesario para consultar y analizar los últimos problemas abiertos de un repositorio de GitHub.

Para registrar un cliente con tipo, se puede usar el método de extensión genérico <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dentro de `Startup.ConfigureServices`, especificando la clase del cliente con tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

El cliente con tipo se registra como transitorio con inserción con dependencias, y se puede insertar y consumir directamente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Si lo prefiere, la configuración de un cliente con nombre se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

El `HttpClient` se puede encapsular completamente dentro de un cliente con nombre. En lugar de exponerlo como una propiedad, se pueden proporcionar métodos públicos que llamen a la instancia de `HttpClient` internamente.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

En el código anterior, el `HttpClient` se almacena como un campo privado. Todo el acceso para realizar llamadas externas pasa por el método `GetRepos`.

### <a name="generated-clients"></a>Clientes generados

`IHttpClientFactory` se puede usar en combinación con otras bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit). Refit es una biblioteca de REST para .NET que convierte las API de REST en interfaces en vivo. Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.

Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Un cliente con tipo se puede agregar usando Refit para generar la implementación:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware de solicitud saliente

`HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes. `IHttpClientFactory` permite definir fácilmente los controladores que se usarán en cada cliente con nombre. Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente. Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida. Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core. Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.

Para crear un controlador, defina una clase que se derive de <xref:System.Net.Http.DelegatingHandler>. Invalide el método `SendAsync` para ejecutar el código antes de pasar la solicitud al siguiente controlador de la canalización:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

El código anterior define un controlador básico. Comprueba si se ha incluido un encabezado `X-API-KEY` en la solicitud. Si no está presente, puede evitar la llamada HTTP y devolver una respuesta adecuada.

Durante el registro, se pueden agregar uno o varios controladores a la configuración de una instancia de `HttpClient`. Esta tarea se realiza a través de métodos de extensión en <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias. `IHttpClientFactory` crea un ámbito de inserción de dependencias independiente para cada controlador. Los controladores pueden depender de servicios de cualquier ámbito. Los servicios de los que dependen los controladores se eliminan cuando se elimina el controlador.

Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, pasando el tipo del controlador.

Se pueden registrar varios controladores en el orden en que deben ejecutarse. Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:

* Pase datos al controlador usando `HttpRequestMessage.Properties`.
* Use `IHttpContextAccessor` para acceder a la solicitud actual.
* Cree un objeto de almacenamiento `AsyncLocal` personalizado para pasar los datos.

## <a name="use-polly-based-handlers"></a>Usar controladores basados en Polly

`IHttpClientFactory` se integra con una biblioteca de terceros muy conocida denominada [Polly](https://github.com/App-vNext/Polly). Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET. Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.

Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas. Las extensiones de Polly:

* permiten agregar controladores basados en Polly a los clientes;
* se pueden usar tras instalar el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/), aunque este no está incluido en la plataforma compartida ASP.NET Core.

### <a name="handle-transient-faults"></a>Control de errores transitorios

Los errores más comunes se producen cuando las llamadas HTTP externas son transitorias. Por ello, se incluye un método de extensión muy práctico denominado `AddTransientHttpErrorPolicy`, que permite definir una directiva para controlar los errores transitorios. Las directivas que se configuran con este método de extensión controlan `HttpRequestException`, las respuestas HTTP 5xx y las respuestas HTTP 408.

La extensión `AddTransientHttpErrorPolicy` se puede usar en `Startup.ConfigureServices`. La extensión da acceso a un objeto `PolicyBuilder`, configurado para controlar los errores que pueden constituir un posible error transitorio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

En el código anterior, se define una directiva `WaitAndRetryAsync`. Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.

### <a name="dynamically-select-policies"></a>Seleccionar directivas dinámicamente

Existen más métodos de extensión que pueden servir para agregar controladores basados en Polly. Una de esas extensiones es `AddPolicyHandler`, que tiene varias sobrecargas. Una de esas sobrecargas permite inspeccionar la solicitud al dilucidar qué directiva aplicar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos. En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Agregar varios controladores de Polly

Es habitual anidar directivas de Polly para proporcionar una funcionalidad mejorada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

En el ejemplo anterior, se agregan dos controladores. En el primer ejemplo se usa la extensión `AddTransientHttpErrorPolicy` para agregar una directiva de reintento. Las solicitudes con error se reintentan hasta tres veces. La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor. Las solicitudes externas subsiguientes se bloquean durante 30 segundos si se producen cinco intentos infructuosos seguidos. Las directivas de interruptor tienen estado. Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.

### <a name="add-policies-from-the-polly-registry"></a>Agregar directivas desde el Registro de Polly

Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`. Se proporciona un método de extensión que permite agregar un controlador por medio de una directiva del Registro:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

En el código anterior, se registran dos directivas cuando se agrega `PolicyRegistry` a `ServiceCollection`. Para usar una directiva del Registro, se usa el método `AddPolicyHandlerFromRegistry` pasando el nombre de la directiva que se va a aplicar.

Encontrará más información sobre `IHttpClientFactory` y las integraciones de Polly en la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient y administración de la duración

Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`. Hay un controlador <xref:System.Net.Http.HttpMessageHandler> por cliente con nombre. La fábrica administra la duración de las instancias de `HttpMessageHandler`.

`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos. Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.

Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes. Crear más controladores de los necesarios puede provocar retrasos en la conexión. Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede ser un obstáculo a la hora de reaccionar ante los cambios de DNS.

La duración de controlador predeterminada es dos minutos. El valor predeterminado se puede reemplazar individualmente en cada cliente con nombre. Para ello, llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> en el `IHttpClientBuilder` que se devuelve cuando se crea el cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

No hace falta eliminar el cliente, ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`. Normalmente, las instancias de `HttpClient` pueden tratarse como objetos de .NET que no requieren eliminación.

Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`. Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas a IHttpClientFactory

El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:

* Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.
* Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.

Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.

- Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.
- Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.

Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.

- `SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`. Este uso compartido impide el agotamiento del socket.
- `SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten. El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto. En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:

 - Deshabilitar el control automático de las cookies
 - Evitar `IHttpClientFactory`

Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registro

Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes. Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados. El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.

La categoría de registro usada en cada cliente incluye el nombre del cliente. Así, por ejemplo, un cliente llamado *MyNamedClient* registra mensajes con una categoría `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud. En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud. En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.

El registro también se produce dentro de la canalización de controlador de la solicitud. En nuestro caso de ejemplo de *MyNamedClient*, esos mensajes se registran en la categoría de registro `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que la solicitud se envíe por la red. En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.

Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización. Esto puede englobar cambios, por ejemplo, en los encabezados de solicitud o en el código de estado de la respuesta.

Si el nombre del cliente se incluye en la categoría de registro, dicho registro se podrá filtrar para encontrar clientes con nombre específicos cuando sea necesario.

## <a name="configure-the-httpmessagehandler"></a>Configurar HttpMessageHandler

Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.

Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo. Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado. Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Uso de IHttpClientFactory en una aplicación de consola

En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

En el ejemplo siguiente:

* <xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).
* `MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`. `HttpClient` se utiliza para recuperar una página web.
* `Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Recursos adicionales

* [Uso de HttpClientFactory para implementar solicitudes HTTP resistentes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementación del patrón de interruptor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) y [Steve Gordon](https://github.com/stevejgordon)

Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación. Esto reporta las siguientes ventajas:

* Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas. Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a [GitHub](https://github.com/). y, de igual modo, registrar otro cliente predeterminado para otros fines.
* Codifica el concepto de middleware saliente a través de controladores de delegación en `HttpClient` y proporciona extensiones para middleware basado en Polly para poder sacar partido de este.
* Administra la agrupación y duración de las instancias de `HttpClientMessageHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.
* Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Los proyectos para .NET Framework requieren instalar el paquete NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/). Los proyectos para .NET Core y que hagan referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ya incluyen el paquete `Microsoft.Extensions.Http`.

## <a name="consumption-patterns"></a>Patrones de consumo

`IHttpClientFactory` se puede usar de varias formas en una aplicación:

* [Uso básico](#basic-usage)
* [Clientes con nombre](#named-clients)
* [Clientes con tipo](#typed-clients)
* [Clientes generados](#generated-clients)

Ninguno de ellos es rigurosamente superior a otro, sino que el mejor método dependerá de las restricciones particulares de la aplicación.

### <a name="basic-usage"></a>Uso básico

Se puede registrar un `IHttpClientFactory` llamando al método de extensión `AddHttpClient` en `IServiceCollection`, dentro del método `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Una vez registrado, el código puede aceptar una interfaz `IHttpClientFactory` en cualquier parte donde se puedan insertar servicios por medio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). `IHttpClientFactory` se puede usar para crear una instancia de `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Cuando `IHttpClientFactory` se usa de este modo, constituye una excelente manera de refactorizar una aplicación existente. No tiene efecto alguno en la forma en que `HttpClient` se usa. En aquellos sitios en los que ya se hayan creado instancias de `HttpClient`, reemplace esas apariciones por una llamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes con nombre

Si una aplicación necesita usar `HttpClient` de diversas maneras, cada una con una configuración diferente, una opción consiste en usar **clientes con nombre**. La configuración de un `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

En el código anterior, se llama a `AddHttpClient` usando el nombre *github*. Este cliente tiene aplicadas algunas configuraciones predeterminadas, a saber, la dirección base y dos encabezados necesarios para trabajar con la API de GitHub.

Cada vez que se llama a `CreateClient`, se crea otra instancia de `HttpClient` y se llama a la acción de configuración.

Para consumir un cliente con nombre, se puede pasar un parámetro de cadena a `CreateClient`. Especifique el nombre del cliente que se va a crear:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

En el código anterior, no es necesario especificar un nombre de host en la solicitud. Basta con pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.

### <a name="typed-clients"></a>Clientes con tipo

Clientes con tipo:

* Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.
* Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.
* Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él. Por ejemplo, el mismo cliente con tipo se puede usar para un punto de conexión back-end único y encapsular toda la lógica que se ocupa de ese punto de conexión.
* Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.

Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

En el código anterior, la configuración se mueve al cliente con tipo. El objeto `HttpClient` se expone como una propiedad pública. Se pueden definir métodos específicos de API que exponen la funcionalidad `HttpClient`. El método `GetAspNetDocsIssues` encapsula el código necesario para consultar y analizar los últimos problemas abiertos de un repositorio de GitHub.

Para registrar un cliente con tipo, se puede usar el método de extensión genérico <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dentro de `Startup.ConfigureServices`, especificando la clase del cliente con tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

El cliente con tipo se registra como transitorio con inserción con dependencias, y se puede insertar y consumir directamente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Si lo prefiere, la configuración de un cliente con nombre se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

El `HttpClient` se puede encapsular completamente dentro de un cliente con nombre. En lugar de exponerlo como una propiedad, se pueden proporcionar métodos públicos que llamen a la instancia de `HttpClient` internamente.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

En el código anterior, el `HttpClient` se almacena como un campo privado. Todo el acceso para realizar llamadas externas pasa por el método `GetRepos`.

### <a name="generated-clients"></a>Clientes generados

`IHttpClientFactory` se puede usar en combinación con otras bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit). Refit es una biblioteca de REST para .NET que convierte las API de REST en interfaces en vivo. Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.

Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Un cliente con tipo se puede agregar usando Refit para generar la implementación:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware de solicitud saliente

`HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes. `IHttpClientFactory` permite definir fácilmente los controladores que se usarán en cada cliente con nombre. Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente. Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida. Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core. Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.

Para crear un controlador, defina una clase que se derive de <xref:System.Net.Http.DelegatingHandler>. Invalide el método `SendAsync` para ejecutar el código antes de pasar la solicitud al siguiente controlador de la canalización:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

El código anterior define un controlador básico. Comprueba si se ha incluido un encabezado `X-API-KEY` en la solicitud. Si no está presente, puede evitar la llamada HTTP y devolver una respuesta adecuada.

Durante el registro, se pueden agregar uno o varios controladores a la configuración de una instancia de `HttpClient`. Esta tarea se realiza a través de métodos de extensión en <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias. El controlador **debe** estar registrado en la inserción de dependencias como servicio transitorio, nunca como servicio con ámbito. Si el controlador se registra como servicio con ámbito y se pueden eliminar los servicios de los que depende el controlador:

* Los servicios del controlador se pueden eliminar antes de que el controlador deje de estar en el ámbito.
* Los servicios del controlador que se eliminen provocarán un error en él.

Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, con lo que se pasa el tipo de controlador.

Se pueden registrar varios controladores en el orden en que deben ejecutarse. Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:

* Pase datos al controlador usando `HttpRequestMessage.Properties`.
* Use `IHttpContextAccessor` para acceder a la solicitud actual.
* Cree un objeto de almacenamiento `AsyncLocal` personalizado para pasar los datos.

## <a name="use-polly-based-handlers"></a>Usar controladores basados en Polly

`IHttpClientFactory` se integra con una biblioteca de terceros muy conocida denominada [Polly](https://github.com/App-vNext/Polly). Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET. Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.

Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas. Las extensiones de Polly:

* permiten agregar controladores basados en Polly a los clientes;
* se pueden usar tras instalar el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/), aunque este no está incluido en la plataforma compartida ASP.NET Core.

### <a name="handle-transient-faults"></a>Control de errores transitorios

Los errores más comunes se producen cuando las llamadas HTTP externas son transitorias. Por ello, se incluye un método de extensión muy práctico denominado `AddTransientHttpErrorPolicy`, que permite definir una directiva para controlar los errores transitorios. Las directivas que se configuran con este método de extensión controlan `HttpRequestException`, las respuestas HTTP 5xx y las respuestas HTTP 408.

La extensión `AddTransientHttpErrorPolicy` se puede usar en `Startup.ConfigureServices`. La extensión da acceso a un objeto `PolicyBuilder`, configurado para controlar los errores que pueden constituir un posible error transitorio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

En el código anterior, se define una directiva `WaitAndRetryAsync`. Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.

### <a name="dynamically-select-policies"></a>Seleccionar directivas dinámicamente

Existen más métodos de extensión que pueden servir para agregar controladores basados en Polly. Una de esas extensiones es `AddPolicyHandler`, que tiene varias sobrecargas. Una de esas sobrecargas permite inspeccionar la solicitud al dilucidar qué directiva aplicar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos. En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Agregar varios controladores de Polly

Es habitual anidar directivas de Polly para proporcionar una funcionalidad mejorada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

En el ejemplo anterior, se agregan dos controladores. En el primer ejemplo se usa la extensión `AddTransientHttpErrorPolicy` para agregar una directiva de reintento. Las solicitudes con error se reintentan hasta tres veces. La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor. Las solicitudes externas subsiguientes se bloquean durante 30 segundos si se producen cinco intentos infructuosos seguidos. Las directivas de interruptor tienen estado. Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.

### <a name="add-policies-from-the-polly-registry"></a>Agregar directivas desde el Registro de Polly

Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`. Se proporciona un método de extensión que permite agregar un controlador por medio de una directiva del Registro:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

En el código anterior, se registran dos directivas cuando se agrega `PolicyRegistry` a `ServiceCollection`. Para usar una directiva del Registro, se usa el método `AddPolicyHandlerFromRegistry` pasando el nombre de la directiva que se va a aplicar.

Encontrará más información sobre `IHttpClientFactory` y las integraciones de Polly en la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient y administración de la duración

Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`. Hay un controlador <xref:System.Net.Http.HttpMessageHandler> por cliente con nombre. La fábrica administra la duración de las instancias de `HttpMessageHandler`.

`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos. Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.

Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes. Crear más controladores de los necesarios puede provocar retrasos en la conexión. Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede ser un obstáculo a la hora de reaccionar ante los cambios de DNS.

La duración de controlador predeterminada es dos minutos. El valor predeterminado se puede reemplazar individualmente en cada cliente con nombre. Para ello, llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> en el `IHttpClientBuilder` que se devuelve cuando se crea el cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

No hace falta eliminar el cliente, ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`. Normalmente, las instancias de `HttpClient` pueden tratarse como objetos de .NET que no requieren eliminación.

Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`. Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas a IHttpClientFactory

El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:

* Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.
* Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.

Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.

- Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.
- Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.

Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.

- `SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`. Este uso compartido impide el agotamiento del socket.
- `SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten. El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto. En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:

 - Deshabilitar el control automático de las cookies
 - Evitar `IHttpClientFactory`

Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registro

Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes. Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados. El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.

La categoría de registro usada en cada cliente incluye el nombre del cliente. Así, por ejemplo, un cliente llamado *MyNamedClient* registra mensajes con una categoría `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud. En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud. En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.

El registro también se produce dentro de la canalización de controlador de la solicitud. En nuestro caso de ejemplo de *MyNamedClient*, esos mensajes se registran en la categoría de registro `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que la solicitud se envíe por la red. En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.

Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización. Esto puede englobar cambios, por ejemplo, en los encabezados de solicitud o en el código de estado de la respuesta.

Si el nombre del cliente se incluye en la categoría de registro, dicho registro se podrá filtrar para encontrar clientes con nombre específicos cuando sea necesario.

## <a name="configure-the-httpmessagehandler"></a>Configurar HttpMessageHandler

Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.

Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo. Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado. Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Uso de IHttpClientFactory en una aplicación de consola

En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

En el ejemplo siguiente:

* <xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).
* `MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`. `HttpClient` se utiliza para recuperar una página web.
* `Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware de propagación de encabezados

La propagación de encabezado es un middleware compatible con la comunidad que se usa para propagar encabezados HTTP de la solicitud entrante a las solicitudes de cliente HTTP salientes. Para utilizar la propagación de encabezados:

* Haga referencia al puerto de comunidad admitido del paquete [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 y las versiones posteriores admiten [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Configure el middleware y `HttpClient` en `Startup`:

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* El cliente incluye los encabezados configurados en las solicitudes salientes:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Recursos adicionales

* [Uso de HttpClientFactory para implementar solicitudes HTTP resistentes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementación del patrón de interruptor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
