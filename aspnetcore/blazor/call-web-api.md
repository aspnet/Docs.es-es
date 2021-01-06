---
title: Llamada a una API web desde Blazor WebAssembly de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo llamar a una API web desde una aplicación Blazor WebAssembly mediante asistentes de JSON, incluida la realización de solicitudes de uso compartido de recursos entre orígenes (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 85b3ded6ec25310a573e99cbedf0df005d92bdbe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93234418"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>Llamada a una API web desde Blazor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) y [Juan De la Cruz](https://github.com/juandelacruz23)

> [!NOTE]
> Este tema se aplica a Blazor WebAssembly. Las aplicaciones [Blazor Server](xref:blazor/hosting-models#blazor-server) llaman a las API web mediante instancias de <xref:System.Net.Http.HttpClient>, creadas normalmente con <xref:System.Net.Http.IHttpClientFactory>. Para obtener las instrucciones correspondientes a Blazor Server, vea <xref:fundamentals/http-requests>.

Las aplicaciones [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) llaman a las API web mediante un servicio <xref:System.Net.Http.HttpClient> preconfigurado. Redacte las solicitudes, que pueden incluir opciones [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, mediante asistentes de JSON de Blazor o con <xref:System.Net.Http.HttpRequestMessage>. El servicio <xref:System.Net.Http.HttpClient> en las aplicaciones Blazor WebAssembly se centra en realizar solicitudes de vuelta al servidor de origen. Las instrucciones de este tema solo se aplican a las aplicaciones Blazor WebAssembly.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample)): Seleccione la aplicación `BlazorWebAssemblySample`.

Vea los componentes siguientes en la aplicación de ejemplo `BlazorWebAssemblySample`:

* Llamada a la API web (`Pages/CallWebAPI.razor`)
* Evaluador de solicitud HTTP (`Components/HTTPRequestTester.razor`)

## <a name="packages"></a>Paquetes

Haga referencia al paquete NuGet [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) en el archivo del proyecto.

## <a name="add-the-httpclient-service"></a>Agregar el servicio HttpClient

En `Program.Main`, agregue un servicio <xref:System.Net.Http.HttpClient> si aún no existe:

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient y asistentes de JSON

En una aplicación Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) está disponible como un servicio preconfigurado para realizar solicitudes de vuelta al servidor de origen.

De forma predeterminada, una aplicación de servidor Blazor Server no incluye un servicio <xref:System.Net.Http.HttpClient>. Proporcione un servicio <xref:System.Net.Http.HttpClient> a la aplicación mediante la [infraestructura de fábrica de `HttpClient`](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient> y los asistentes de JSON también se usan para llamar a puntos de conexión de API web de terceros. <xref:System.Net.Http.HttpClient> se implementa mediante [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) del explorador y está sujeto a sus limitaciones, incluido el cumplimiento de la directiva del mismo origen.

La dirección base del cliente se establece en la dirección del servidor de origen. Inserte una instancia de <xref:System.Net.Http.HttpClient> mediante la directiva [`@inject`](xref:mvc/views/razor#inject):

```razor
@using System.Net.Http
@inject HttpClient Http
```

En los ejemplos siguientes, una API web Todo procesa operaciones de creación, lectura, actualización y eliminación (CRUD). Los ejemplos se basan en una clase `TodoItem` que almacena lo siguiente:

* Id. (`Id`, `long`): identificador único del elemento.
* Nombre (`Name`, `string`): Nombre del elemento.
* Estado (`IsComplete`, `bool`): indicación de si el elemento Todo ha finalizado.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Los métodos auxiliares de JSON envían solicitudes a un URI (una API web en los ejemplos siguientes) y procesan la respuesta:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: envía una solicitud HTTP GET y analiza el cuerpo de la respuesta JSON para crear un objeto.

  En el código siguiente, el componente muestra el elemento `todoItems`. El método `GetTodoItems` se desencadena cuando finaliza la representación del componente ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)). Vea la aplicación de ejemplo para obtener un ejemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: envía una solicitud HTTP POST, incluido el contenido con codificación JSON, y analiza el cuerpo de respuesta JSON para crear un objeto.

  En el código siguiente, un elemento enlazado del componente proporciona `newItemName`. El método `AddItem` se desencadena al seleccionar un elemento `<button>`. Vea la aplicación de ejemplo para obtener un ejemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Las llamadas a <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> devuelven <xref:System.Net.Http.HttpResponseMessage>. Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión `ReadFromJsonAsync<T>`:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: envía una solicitud HTTP PUT, incluido el contenido con codificación JSON.

  En el código siguiente, los elementos enlazados del componente proporcionan valores `editItem` para `Name` e `IsCompleted`. El valor `Id` del elemento se establece cuando el elemento está seleccionado en otra parte de la interfaz de usuario y se llama a `EditItem`. El método `SaveItem` se desencadena al seleccionar un objeto `<button>` Guardar. Vea la aplicación de ejemplo para obtener un ejemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Las llamadas a <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> devuelven <xref:System.Net.Http.HttpResponseMessage>. Para deserializar el contenido JSON del mensaje de respuesta, use el método de extensión <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> incluye métodos de extensión adicionales para enviar solicitudes HTTP y recibir respuestas HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> se usa para enviar una solicitud HTTP DELETE a una API web.

En el código siguiente, el elemento `<button>` Delete llama al método `DeleteItem`. El elemento `<input>` enlazado proporciona el valor `id` del elemento que se va a eliminar. Vea la aplicación de ejemplo para obtener un ejemplo completo.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>HttpClient con nombre con IHttpClientFactory

Se admiten servicios <xref:System.Net.Http.IHttpClientFactory> y la configuración de una instancia de <xref:System.Net.Http.HttpClient> con nombre.

Haga referencia al paquete NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) en el archivo del proyecto.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Componente `FetchData` (`Pages/FetchData.razor`):

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>HttpClient con tipo

La instancia de <xref:System.Net.Http.HttpClient> con tipo usa una o varias instancias de <xref:System.Net.Http.HttpClient> de la aplicación, ya sean predeterminadas o con nombre, para devolver datos de uno o varios puntos de conexión de la API web.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Los componentes insertan la instancia de <xref:System.Net.Http.HttpClient> con tipo para llamar a la API web.

Componente `FetchData` (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` y `HttpRequestMessage` con las opciones de solicitud de Fetch API

Al ejecutar WebAssembly en una aplicación Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) ([documentación de API](xref:System.Net.Http.HttpClient)) y <xref:System.Net.Http.HttpRequestMessage> se pueden usar para personalizar las solicitudes. Por ejemplo, se puede especificar el método HTTP y los encabezados de solicitud. El siguiente componente realiza una solicitud `POST` a un punto de conexión de API de lista de tareas pendientes en el servidor y muestra el cuerpo de la respuesta:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

En la implementación de <xref:System.Net.Http.HttpClient> de WebAssembly de .NET se usa [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Fetch permite configurar varias [opciones específicas de la solicitud](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Se pueden configurar opciones de solicitud de captura HTTP con los métodos de extensión <xref:System.Net.Http.HttpRequestMessage> que se muestran en la siguiente tabla.

| Método de extensión | Propiedad de solicitud de captura |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Se pueden establecer más opciones usando el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>, que es más genérico.
 
Normalmente, la respuesta HTTP se almacena en búfer en una aplicación Blazor WebAssembly para habilitar la compatibilidad con las lecturas de sincronización en el contenido de la respuesta. Para habilitar la compatibilidad con la transmisión de respuestas, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> en la solicitud.

Para incluir credenciales en una solicitud entre orígenes, use el método de extensión <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Para obtener más información sobre las opciones de Fetch API, vea [Documentación web de MDN: WindowOrWorkerGlobalScope.fetch(): parámetros](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Control de errores

Cuando se producen errores al interactuar con una API web, pueden controlarse mediante código de desarrollador. Por ejemplo, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera una respuesta JSON de la API del servidor con un `Content-Type` de `application/json`. Si la respuesta no está en formato JSON, la validación del contenido producirá una excepción <xref:System.NotSupportedException>.

En el ejemplo siguiente, el punto de conexión del URI para la solicitud de datos de previsión meteorológica está mal escrito. El URI debería ser `WeatherForecast`, pero en la llamada aparece como `WeatherForcast` (falta una "e").

La llamada a <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera que se devuelva JSON, pero el servidor devuelve HTML para una excepción no controlada en el servidor con un `Content-Type` de `text/html`. La excepción no controlada se produce en el servidor, puesto que no se encuentra la ruta de acceso y el middleware no puede proporcionar una página o vista para la solicitud.

En <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> en el cliente, se produce una excepción <xref:System.NotSupportedException> cuando el contenido de la respuesta se valida como distinto de JSON. La excepción se captura en el bloque `catch`, donde la lógica personalizada podría registrar el error o mostrar al usuario un mensaje de error descriptivo:

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> El ejemplo anterior sirve de demostración. Se puede configurar una aplicación de servidor de API web para que devuelva JSON incluso cuando no existe un punto de conexión o cuando se produce una excepción no controlada en el servidor.

Para obtener más información, vea <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Uso compartido de recursos entre orígenes (CORS)

La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que ha proporcionado esa página web. Esta restricción se denomina *directiva de mismo origen*. La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio. Para realizar solicitudes desde el explorador a un punto de conexión con un origen diferente, el *punto de conexión* debe habilitar el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/).

En la aplicación de ejemplo [Blazor WebAssembly (BlazorBlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) se muestra el uso de CORS en el componente de llamada a la API web (`Pages/CallWebAPI.razor`).

Para más información sobre CORS con solicitudes seguras en Blazor aplicaciones, consulte <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.

Para obtener información general sobre CORS con aplicaciones de ASP.NET Core, consulte <xref:security/cors>.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/security/webassembly/additional-scenarios>: incluye cobertura sobre el uso de <xref:System.Net.Http.HttpClient> para hacer solicitudes seguras de API web.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuración de puntos de conexión HTTPS de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Uso compartido de recursos entre orígenes (CORS) en W3C](https://www.w3.org/TR/cors/)
