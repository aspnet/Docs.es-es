---
title: Realización de solicitudes HTTP mediante IHttpClientFactory en ASP.NET Core
author: stevejgordon
description: Obtenga información sobre cómo usar la interfaz IHttpClientFactory para administrar instancias de HttpClient lógicas en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
uid: fundamentals/http-requests
ms.openlocfilehash: ef6ed0911c2615ea543b1c2cea31a9a453f0c604
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017238"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="3f8b7-103">Realización de solicitudes HTTP mediante IHttpClientFactory en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f8b7-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f8b7-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3f8b7-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="3f8b7-105">Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3f8b7-106">`IHttpClientFactory` ofrece las ventajas siguientes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="3f8b7-107">Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-108">Por ejemplo, se podría registrar un cliente *github* y configurarlo para acceder a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3f8b7-109">Se puede registrar un cliente predeterminado para el acceso general.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="3f8b7-110">Codifica el concepto de middleware de salida a través de la delegación de controladores en `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="3f8b7-111">Proporciona extensiones para el middleware basado en Polly a fin de aprovechar los controladores de delegación en `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="3f8b7-112">Administra la agrupación y la duración de las instancias de `HttpClientMessageHandler` subyacentes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="3f8b7-113">La administración automática evita los problemas comunes de DNS (Sistema de nombres de dominio) que se producen al administrar la duración de `HttpClient` de forma manual.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3f8b7-114">Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3f8b7-115">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3f8b7-116">En el código de ejemplo de la versión este tema se usa <xref:System.Text.Json> para deserializar el contenido JSON devuelto en las respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="3f8b7-117">Para obtener ejemplos en los que se usan `Json.NET` y `ReadAsAsync<T>`, utilice el selector de versión para seleccionar una versión 2.x de este tema.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3f8b7-118">Patrones de consumo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-118">Consumption patterns</span></span>

<span data-ttu-id="3f8b7-119">`IHttpClientFactory` se puede usar de varias formas en una aplicación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3f8b7-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3f8b7-121">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3f8b7-122">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3f8b7-123">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3f8b7-124">El mejor enfoque depende de los requisitos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3f8b7-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-125">Basic usage</span></span>

<span data-ttu-id="3f8b7-126">`IHttpClientFactory` se puede registrar mediante una llamada a `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3f8b7-127">Se puede solicitar una instancia de `IHttpClientFactory` mediante la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3f8b7-128">En el código siguiente se usa `IHttpClientFactory` para crear una instancia de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3f8b7-129">El uso de `IHttpClientFactory` como en el ejemplo anterior es una buena manera de refactorizar una aplicación existente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="3f8b7-130">No tiene efecto alguno en la forma de usar `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="3f8b7-131">En aquellos sitios de una aplicación existente en los que ya se hayan creado instancias de `HttpClient`, reemplace esas repeticiones por llamadas a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3f8b7-132">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-132">Named clients</span></span>

<span data-ttu-id="3f8b7-133">Los clientes con nombre son una buena opción cuando:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="3f8b7-134">La aplicación requiere muchos usos distintos de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="3f8b7-135">Muchas instancias `HttpClient` de tienen otra configuración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="3f8b7-136">La configuración de un objeto `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3f8b7-137">En el código anterior, el cliente está configurado con:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="3f8b7-138">La dirección base. `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="3f8b7-139">Dos encabezados necesarios para trabajar con la API de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="3f8b7-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="3f8b7-140">CreateClient</span></span>

<span data-ttu-id="3f8b7-141">Cada vez que se llama a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="3f8b7-142">Se crea una instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="3f8b7-143">Se llama a la acción de configuración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-143">The configuration action is called.</span></span>

<span data-ttu-id="3f8b7-144">Para crear un cliente con nombre, pase su nombre a `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3f8b7-145">En el código anterior, no es necesario especificar un nombre de host en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3f8b7-146">El código puede pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3f8b7-147">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-147">Typed clients</span></span>

<span data-ttu-id="3f8b7-148">Clientes con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-148">Typed clients:</span></span>

* <span data-ttu-id="3f8b7-149">Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3f8b7-150">Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3f8b7-151">Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3f8b7-152">Por ejemplo, es posible usar un solo cliente con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="3f8b7-153">Para un único punto de conexión de back-end.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="3f8b7-154">Para encapsular toda la lógica relacionada con el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="3f8b7-155">Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="3f8b7-156">Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3f8b7-157">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-157">In the preceding code:</span></span>

* <span data-ttu-id="3f8b7-158">La configuración se mueve al cliente con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="3f8b7-159">El objeto `HttpClient` se expone como una propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="3f8b7-160">Se pueden crear métodos específicos de la API que exponen la funcionalidad de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3f8b7-161">Por ejemplo, el método `GetAspNetDocsIssues` encapsula el código para recuperar incidencias abiertas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="3f8b7-162">En el código siguiente se llama a <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> en `Startup.ConfigureServices` para registrar una clase de cliente con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3f8b7-163">El cliente con tipo se registra como transitorio con inserción con dependencias,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3f8b7-164">En el código anterior, `AddHttpClient` registra `GitHubService` como servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="3f8b7-165">Este registro usa un Factory Method para:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="3f8b7-166">Crea una instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="3f8b7-167">Cree una instancia de `GitHubService`, pasando la instancia de `HttpClient` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="3f8b7-168">y se puede insertar y consumir directamente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3f8b7-169">La configuración de un cliente con tipo se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3f8b7-170">`HttpClient` se puede encapsular dentro de un cliente con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="3f8b7-171">En lugar de exponerlo como una propiedad, defina un método que llame a la instancia de `HttpClient` internamente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3f8b7-172">En el código anterior, `HttpClient` se almacena en un campo privado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="3f8b7-173">El acceso a `HttpClient` se realiza mediante el método `GetRepos` público.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3f8b7-174">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-174">Generated clients</span></span>

<span data-ttu-id="3f8b7-175">`IHttpClientFactory` se puede usar en combinación con bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3f8b7-176">Refit es una biblioteca de REST para .NET</span><span class="sxs-lookup"><span data-stu-id="3f8b7-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3f8b7-177">que convierte las API de REST en interfaces en vivo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3f8b7-178">Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3f8b7-179">Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3f8b7-180">Un cliente con tipo se puede agregar usando Refit para generar la implementación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3f8b7-181">La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="3f8b7-182">Realización de solicitudes POST, PUT y DELETE</span><span class="sxs-lookup"><span data-stu-id="3f8b7-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="3f8b7-183">En los ejemplos anteriores, todas las solicitudes HTTP usan el verbo HTTP de GET.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="3f8b7-184">`HttpClient` también admite otros verbos HTTP; por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="3f8b7-185">POST</span><span class="sxs-lookup"><span data-stu-id="3f8b7-185">POST</span></span>
* <span data-ttu-id="3f8b7-186">PUT</span><span class="sxs-lookup"><span data-stu-id="3f8b7-186">PUT</span></span>
* <span data-ttu-id="3f8b7-187">SUPRIMIR</span><span class="sxs-lookup"><span data-stu-id="3f8b7-187">DELETE</span></span>
* <span data-ttu-id="3f8b7-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="3f8b7-188">PATCH</span></span>

<span data-ttu-id="3f8b7-189">Para obtener una lista completa de los verbos HTTP admitidos, vea <xref:System.Net.Http.HttpMethod>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="3f8b7-190">En el ejemplo siguiente se muestra cómo hacer una solicitud POST HTTP:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="3f8b7-191">En el código anterior, el método `CreateItemAsync`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="3f8b7-192">Serializa el parámetro `TodoItem` en JSON mediante `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="3f8b7-193">Usa una instancia de <xref:System.Text.Json.JsonSerializerOptions> para configurar el proceso de serialización.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="3f8b7-194">Crea una instancia de <xref:System.Net.Http.StringContent> a fin de empaquetar el JSON serializado para enviarlo en el cuerpo de la solicitud de HTTP.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="3f8b7-195">Llama a <xref:System.Net.Http.HttpClient.PostAsync%2A> para enviar el contenido de JSON a la URL especificada.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="3f8b7-196">Se trata de una URL relativa que se agrega a [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="3f8b7-197">Llama a <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> para iniciar una excepción si el código de estado de la respuesta no indica que la operación se ha procesado correctamente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="3f8b7-198">`HttpClient` también admite otros tipos de contenido.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="3f8b7-199">Por ejemplo, <xref:System.Net.Http.MultipartContent> y <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="3f8b7-200">Para obtener una lista completa del contenido admitido, vea <xref:System.Net.Http.HttpContent>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="3f8b7-201">En el ejemplo siguiente se muestra una solicitud PUT HTTP:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="3f8b7-202">El código anterior es muy similar al ejemplo de POST.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="3f8b7-203">El método `SaveItemAsync` llama a <xref:System.Net.Http.HttpClient.PutAsync%2A> en lugar de `PostAsync`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="3f8b7-204">En el ejemplo siguiente se muestra una solicitud DELETE HTTP:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="3f8b7-205">En el código anterior, el método `DeleteItemAsync` llama a <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="3f8b7-206">Debido a que las solicitudes DELETE HTTP normalmente no contienen ningún cuerpo, el método `DeleteAsync` no proporciona ninguna sobrecarga que acepte una instancia de `HttpContent`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="3f8b7-207">Para obtener más información sobre el uso de verbos HTTP diferentes con `HttpClient`, vea <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3f8b7-208">Middleware de solicitud saliente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-208">Outgoing request middleware</span></span>

<span data-ttu-id="3f8b7-209">`HttpClient` tiene el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3f8b7-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="3f8b7-211">simplifica la definición de controladores que se aplicarán por cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="3f8b7-212">Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3f8b7-213">Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3f8b7-214">Este patrón:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-214">This pattern:</span></span>

  * <span data-ttu-id="3f8b7-215">Es similar a la canalización de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="3f8b7-216">Proporciona un mecanismo para administrar los intereses transversales relacionados con las solicitudes HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="3f8b7-217">el almacenamiento en caché</span><span class="sxs-lookup"><span data-stu-id="3f8b7-217">caching</span></span>
    * <span data-ttu-id="3f8b7-218">el control de errores</span><span class="sxs-lookup"><span data-stu-id="3f8b7-218">error handling</span></span>
    * <span data-ttu-id="3f8b7-219">la serialización</span><span class="sxs-lookup"><span data-stu-id="3f8b7-219">serialization</span></span>
    * <span data-ttu-id="3f8b7-220">el registro</span><span class="sxs-lookup"><span data-stu-id="3f8b7-220">logging</span></span>

<span data-ttu-id="3f8b7-221">Para crear un controlador de delegación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-221">To create a delegating handler:</span></span>

* <span data-ttu-id="3f8b7-222">Derívelo de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="3f8b7-223">Reemplace <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="3f8b7-224">Ejecute el código antes de pasar la solicitud al siguiente controlador de la canalización:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3f8b7-225">El código anterior comprueba si el encabezado `X-API-KEY` está en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="3f8b7-226">Si falta `X-API-KEY`, se devuelve <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="3f8b7-227">Se puede agregar más de un controlador a la configuración de una instancia de `HttpClient` con <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="3f8b7-228">En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3f8b7-229">`IHttpClientFactory` crea un ámbito de inserción de dependencias independiente para cada controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="3f8b7-230">Los controladores pueden depender de servicios de cualquier ámbito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="3f8b7-231">Los servicios de los que dependen los controladores se eliminan cuando se elimina el controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="3f8b7-232">Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, pasando el tipo del controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="3f8b7-233">Se pueden registrar varios controladores en el orden en que deben ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3f8b7-234">Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3f8b7-235">Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3f8b7-236">Pase datos al controlador mediante [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="3f8b7-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acceder a la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="3f8b7-238">Cree un objeto de almacenamiento <xref:System.Threading.AsyncLocal`1> personalizado para pasar los datos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3f8b7-239">Usar controladores basados en Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-239">Use Polly-based handlers</span></span>

<span data-ttu-id="3f8b7-240">`IHttpClientFactory` se integra con la biblioteca de terceros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3f8b7-241">Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3f8b7-242">Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3f8b7-243">Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-244">Las extensiones de Polly permiten agregar controladores basados en Polly a los clientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="3f8b7-245">Polly requiere el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3f8b7-246">Control de errores transitorios</span><span class="sxs-lookup"><span data-stu-id="3f8b7-246">Handle transient faults</span></span>

<span data-ttu-id="3f8b7-247">Los errores se suelen producir cuando las llamadas HTTP externas son transitorias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3f8b7-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite definir una directiva para controlar los errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3f8b7-249">Las directivas configuradas con `AddTransientHttpErrorPolicy` controlan las respuestas siguientes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="3f8b7-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="3f8b7-250">HTTP 5xx</span></span>
* <span data-ttu-id="3f8b7-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="3f8b7-251">HTTP 408</span></span>

<span data-ttu-id="3f8b7-252">`AddTransientHttpErrorPolicy` proporciona acceso a un objeto `PolicyBuilder` configurado para controlar los errores que representan un posible error transitorio:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="3f8b7-253">En el código anterior, se define una directiva `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3f8b7-254">Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3f8b7-255">Seleccionar directivas dinámicamente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-255">Dynamically select policies</span></span>

<span data-ttu-id="3f8b7-256">Los métodos de extensión se proporcionan para agregar controladores basados en Polly, por ejemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="3f8b7-257">La siguiente sobrecarga de `AddPolicyHandler` inspecciona la solicitud para decidir qué directiva se debe aplicar:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3f8b7-258">En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3f8b7-259">En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3f8b7-260">Agregar varios controladores de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="3f8b7-261">Es común anidar las directivas de Polly:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3f8b7-262">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-262">In the preceding example:</span></span>

* <span data-ttu-id="3f8b7-263">Se agregan dos controladores.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-263">Two handlers are added.</span></span>
* <span data-ttu-id="3f8b7-264">El primer controlador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para agregar una directiva de reintentos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="3f8b7-265">Las solicitudes con error se reintentan hasta tres veces.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="3f8b7-266">La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="3f8b7-267">Las solicitudes externas adicionales se bloquean durante 30 segundos si se producen cinco intentos con error seguidos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="3f8b7-268">Las directivas de interruptor tienen estado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3f8b7-269">Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3f8b7-270">Agregar directivas desde el Registro de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="3f8b7-271">Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="3f8b7-272">En el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-272">In the following code:</span></span>

* <span data-ttu-id="3f8b7-273">Se agregan las directivas "regular" y "long".</span><span class="sxs-lookup"><span data-stu-id="3f8b7-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="3f8b7-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> agrega las directivas "regular" y "long" del registro.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="3f8b7-275">Para más información sobre `IHttpClientFactory` y las integraciones de Polly, vea la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3f8b7-276">HttpClient y administración de la duración</span><span class="sxs-lookup"><span data-stu-id="3f8b7-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="3f8b7-277">Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-278">Se crea un objeto <xref:System.Net.Http.HttpMessageHandler> por cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="3f8b7-279">La fábrica administra la duración de las instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3f8b7-280">`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3f8b7-281">Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3f8b7-282">Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3f8b7-283">Crear más controladores de los necesarios puede provocar retrasos en la conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3f8b7-284">Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede impedir que el controlador reaccione ante los cambios de DNS (Sistema de nombres de dominio).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="3f8b7-285">La duración de controlador predeterminada es dos minutos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3f8b7-286">El valor predeterminado se puede reemplazar de forma individual en cada cliente con nombre:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="3f8b7-287">Normalmente, las instancias de `HttpClient` se pueden trata como objetos de .NET que **no** requieren eliminación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="3f8b7-288">ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3f8b7-289">`IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="3f8b7-290">Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-291">Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3f8b7-292">Alternativas a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3f8b7-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3f8b7-293">El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3f8b7-294">Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3f8b7-295">Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3f8b7-296">Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3f8b7-297">Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3f8b7-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3f8b7-299">Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3f8b7-300">Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3f8b7-301">`SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-302">Este uso compartido impide el agotamiento del socket.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3f8b7-303">`SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="3f8b7-304">Cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-304">Cookies</span></span>

<span data-ttu-id="3f8b7-305">Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3f8b7-306">El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3f8b7-307">En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3f8b7-308">Deshabilitación del control automático de cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3f8b7-309">Evitar `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3f8b7-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3f8b7-310">Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3f8b7-311">Registro</span><span class="sxs-lookup"><span data-stu-id="3f8b7-311">Logging</span></span>

<span data-ttu-id="3f8b7-312">Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3f8b7-313">Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="3f8b7-314">El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3f8b7-315">La categoría de registro usada en cada cliente incluye el nombre del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3f8b7-316">Un cliente denominado *MyNamedClient*, por ejemplo, registra mensajes con una categoría de "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="3f8b7-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="3f8b7-317">Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-318">En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3f8b7-319">En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3f8b7-320">El registro también se produce dentro de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-321">En el ejemplo *MyNamedClient*, esos mensajes se registran con la categoría de registro "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="3f8b7-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="3f8b7-322">En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que se envíe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="3f8b7-323">En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3f8b7-324">Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3f8b7-325">Esto puede incluir cambios en los encabezados de solicitud o en el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="3f8b7-326">La inclusión del nombre del cliente en la categoría de registro permite filtrar el registro para clientes con nombre específicos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3f8b7-327">Configurar HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3f8b7-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3f8b7-328">Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3f8b7-329">Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3f8b7-330">Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3f8b7-331">Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3f8b7-332">Uso de IHttpClientFactory en una aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="3f8b7-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3f8b7-333">En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3f8b7-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="3f8b7-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3f8b7-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="3f8b7-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3f8b7-336">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-336">In the following example:</span></span>

* <span data-ttu-id="3f8b7-337"><xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3f8b7-338">`MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3f8b7-339">`HttpClient` se utiliza para recuperar una página web.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3f8b7-340">`Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="3f8b7-341">Middleware de propagación de encabezados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-341">Header propagation middleware</span></span>

<span data-ttu-id="3f8b7-342">La propagación de encabezados es un middleware ASP.NET Core que se usa para propagar encabezados HTTP de la solicitud entrante a las solicitudes de cliente HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="3f8b7-343">Para utilizar la propagación de encabezados:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-343">To use header propagation:</span></span>

* <span data-ttu-id="3f8b7-344">Haga referencia al paquete [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="3f8b7-345">Configure el middleware y `HttpClient` en `Startup`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="3f8b7-346">El cliente incluye los encabezados configurados en las solicitudes salientes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="3f8b7-347">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3f8b7-347">Additional resources</span></span>

* [<span data-ttu-id="3f8b7-348">Uso de HttpClientFactory para implementar solicitudes HTTP resistentes</span><span class="sxs-lookup"><span data-stu-id="3f8b7-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3f8b7-349">Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3f8b7-350">Implementación del patrón de interruptor</span><span class="sxs-lookup"><span data-stu-id="3f8b7-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="3f8b7-351">Procedimiento para serializar y deserializar JSON en .NET</span><span class="sxs-lookup"><span data-stu-id="3f8b7-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3f8b7-352">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) y [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="3f8b7-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="3f8b7-353">Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3f8b7-354">Esto reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-354">It offers the following benefits:</span></span>

* <span data-ttu-id="3f8b7-355">Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-356">Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3f8b7-357">y, de igual modo, registrar otro cliente predeterminado para otros fines.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="3f8b7-358">Codifica el concepto de middleware saliente a través de controladores de delegación en `HttpClient` y proporciona extensiones para middleware basado en Polly para poder sacar partido de este.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="3f8b7-359">Administra la agrupación y duración de las instancias de `HttpClientMessageHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3f8b7-360">Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3f8b7-361">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f8b7-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3f8b7-362">Patrones de consumo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-362">Consumption patterns</span></span>

<span data-ttu-id="3f8b7-363">`IHttpClientFactory` se puede usar de varias formas en una aplicación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3f8b7-364">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3f8b7-365">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3f8b7-366">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3f8b7-367">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3f8b7-368">Ninguno de ellos es rigurosamente superior a otro,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="3f8b7-369">sino que el mejor método dependerá de las restricciones particulares de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3f8b7-370">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-370">Basic usage</span></span>

<span data-ttu-id="3f8b7-371">Se puede registrar un `IHttpClientFactory` llamando al método de extensión `AddHttpClient` en `IServiceCollection`, dentro del método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3f8b7-372">Una vez registrado, el código puede aceptar una interfaz `IHttpClientFactory` en cualquier parte donde se puedan insertar servicios por medio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3f8b7-373">`IHttpClientFactory` se puede usar para crear una instancia de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3f8b7-374">Cuando `IHttpClientFactory` se usa de este modo, constituye una excelente manera de refactorizar una aplicación existente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="3f8b7-375">No tiene efecto alguno en la forma en que `HttpClient` se usa.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="3f8b7-376">En aquellos sitios en los que ya se hayan creado instancias de `HttpClient`, reemplace esas apariciones por una llamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3f8b7-377">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-377">Named clients</span></span>

<span data-ttu-id="3f8b7-378">Si una aplicación necesita usar `HttpClient` de diversas maneras, cada una con una configuración diferente, una opción consiste en usar **clientes con nombre**.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="3f8b7-379">La configuración de un `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3f8b7-380">En el código anterior, se llama a `AddHttpClient` usando el nombre *github*.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="3f8b7-381">Este cliente tiene aplicadas algunas configuraciones predeterminadas, a saber, la dirección base y dos encabezados necesarios para trabajar con la API de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="3f8b7-382">Cada vez que se llama a `CreateClient`, se crea otra instancia de `HttpClient` y se llama a la acción de configuración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="3f8b7-383">Para consumir un cliente con nombre, se puede pasar un parámetro de cadena a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="3f8b7-384">Especifique el nombre del cliente que se va a crear:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3f8b7-385">En el código anterior, no es necesario especificar un nombre de host en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3f8b7-386">Basta con pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3f8b7-387">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-387">Typed clients</span></span>

<span data-ttu-id="3f8b7-388">Clientes con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-388">Typed clients:</span></span>

* <span data-ttu-id="3f8b7-389">Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3f8b7-390">Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3f8b7-391">Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3f8b7-392">Por ejemplo, el mismo cliente con tipo se puede usar para un punto de conexión back-end único y encapsular toda la lógica que se ocupa de ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="3f8b7-393">Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="3f8b7-394">Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="3f8b7-395">En el código anterior, la configuración se mueve al cliente con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="3f8b7-396">El objeto `HttpClient` se expone como una propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="3f8b7-397">Se pueden definir métodos específicos de API que exponen la funcionalidad `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3f8b7-398">El método `GetAspNetDocsIssues` encapsula el código necesario para consultar y analizar los últimos problemas abiertos de un repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="3f8b7-399">Para registrar un cliente con tipo, se puede usar el método de extensión genérico <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dentro de `Startup.ConfigureServices`, especificando la clase del cliente con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3f8b7-400">El cliente con tipo se registra como transitorio con inserción con dependencias,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3f8b7-401">y se puede insertar y consumir directamente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3f8b7-402">Si lo prefiere, la configuración de un cliente con nombre se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3f8b7-403">El `HttpClient` se puede encapsular completamente dentro de un cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="3f8b7-404">En lugar de exponerlo como una propiedad, se pueden proporcionar métodos públicos que llamen a la instancia de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3f8b7-405">En el código anterior, el `HttpClient` se almacena como un campo privado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="3f8b7-406">Todo el acceso para realizar llamadas externas pasa por el método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3f8b7-407">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-407">Generated clients</span></span>

<span data-ttu-id="3f8b7-408">`IHttpClientFactory` se puede usar en combinación con otras bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3f8b7-409">Refit es una biblioteca de REST para .NET</span><span class="sxs-lookup"><span data-stu-id="3f8b7-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3f8b7-410">que convierte las API de REST en interfaces en vivo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3f8b7-411">Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3f8b7-412">Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3f8b7-413">Un cliente con tipo se puede agregar usando Refit para generar la implementación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3f8b7-414">La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3f8b7-415">Middleware de solicitud saliente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-415">Outgoing request middleware</span></span>

<span data-ttu-id="3f8b7-416">`HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3f8b7-417">`IHttpClientFactory` permite definir fácilmente los controladores que se usarán en cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="3f8b7-418">Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3f8b7-419">Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3f8b7-420">Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="3f8b7-421">Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="3f8b7-422">Para crear un controlador, defina una clase que se derive de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="3f8b7-423">Invalide el método `SendAsync` para ejecutar el código antes de pasar la solicitud al siguiente controlador de la canalización:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3f8b7-424">El código anterior define un controlador básico.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="3f8b7-425">Comprueba si se ha incluido un encabezado `X-API-KEY` en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="3f8b7-426">Si no está presente, puede evitar la llamada HTTP y devolver una respuesta adecuada.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="3f8b7-427">Durante el registro, se pueden agregar uno o varios controladores a la configuración de una instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="3f8b7-428">Esta tarea se realiza a través de métodos de extensión en <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="3f8b7-429">En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3f8b7-430">`IHttpClientFactory` crea un ámbito de inserción de dependencias independiente para cada controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="3f8b7-431">Los controladores pueden depender de servicios de cualquier ámbito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="3f8b7-432">Los servicios de los que dependen los controladores se eliminan cuando se elimina el controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="3f8b7-433">Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, pasando el tipo del controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="3f8b7-434">Se pueden registrar varios controladores en el orden en que deben ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3f8b7-435">Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3f8b7-436">Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3f8b7-437">Pase datos al controlador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="3f8b7-438">Use `IHttpContextAccessor` para acceder a la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="3f8b7-439">Cree un objeto de almacenamiento `AsyncLocal` personalizado para pasar los datos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3f8b7-440">Usar controladores basados en Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-440">Use Polly-based handlers</span></span>

<span data-ttu-id="3f8b7-441">`IHttpClientFactory` se integra con una biblioteca de terceros muy conocida denominada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3f8b7-442">Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3f8b7-443">Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3f8b7-444">Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-445">Las extensiones de Polly:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-445">The Polly extensions:</span></span>

* <span data-ttu-id="3f8b7-446">permiten agregar controladores basados en Polly a los clientes;</span><span class="sxs-lookup"><span data-stu-id="3f8b7-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="3f8b7-447">se pueden usar tras instalar el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/),</span><span class="sxs-lookup"><span data-stu-id="3f8b7-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="3f8b7-448">aunque este no está incluido en la plataforma compartida ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3f8b7-449">Control de errores transitorios</span><span class="sxs-lookup"><span data-stu-id="3f8b7-449">Handle transient faults</span></span>

<span data-ttu-id="3f8b7-450">Los errores más comunes se producen cuando las llamadas HTTP externas son transitorias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3f8b7-451">Por ello, se incluye un método de extensión muy práctico denominado `AddTransientHttpErrorPolicy`, que permite definir una directiva para controlar los errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3f8b7-452">Las directivas que se configuran con este método de extensión controlan `HttpRequestException`, las respuestas HTTP 5xx y las respuestas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="3f8b7-453">La extensión `AddTransientHttpErrorPolicy` se puede usar en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3f8b7-454">La extensión da acceso a un objeto `PolicyBuilder`, configurado para controlar los errores que pueden constituir un posible error transitorio:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="3f8b7-455">En el código anterior, se define una directiva `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3f8b7-456">Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3f8b7-457">Seleccionar directivas dinámicamente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-457">Dynamically select policies</span></span>

<span data-ttu-id="3f8b7-458">Existen más métodos de extensión que pueden servir para agregar controladores basados en Polly.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="3f8b7-459">Una de esas extensiones es `AddPolicyHandler`, que tiene varias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="3f8b7-460">Una de esas sobrecargas permite inspeccionar la solicitud al dilucidar qué directiva aplicar:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3f8b7-461">En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3f8b7-462">En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3f8b7-463">Agregar varios controladores de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="3f8b7-464">Es habitual anidar directivas de Polly para proporcionar una funcionalidad mejorada:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3f8b7-465">En el ejemplo anterior, se agregan dos controladores.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="3f8b7-466">En el primer ejemplo se usa la extensión `AddTransientHttpErrorPolicy` para agregar una directiva de reintento.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="3f8b7-467">Las solicitudes con error se reintentan hasta tres veces.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="3f8b7-468">La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="3f8b7-469">Las solicitudes externas subsiguientes se bloquean durante 30 segundos si se producen cinco intentos infructuosos seguidos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="3f8b7-470">Las directivas de interruptor tienen estado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3f8b7-471">Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3f8b7-472">Agregar directivas desde el Registro de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="3f8b7-473">Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="3f8b7-474">Se proporciona un método de extensión que permite agregar un controlador por medio de una directiva del Registro:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="3f8b7-475">En el código anterior, se registran dos directivas cuando se agrega `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="3f8b7-476">Para usar una directiva del Registro, se usa el método `AddPolicyHandlerFromRegistry` pasando el nombre de la directiva que se va a aplicar.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="3f8b7-477">Encontrará más información sobre `IHttpClientFactory` y las integraciones de Polly en la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3f8b7-478">HttpClient y administración de la duración</span><span class="sxs-lookup"><span data-stu-id="3f8b7-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="3f8b7-479">Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-480">Hay un controlador <xref:System.Net.Http.HttpMessageHandler> por cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="3f8b7-481">La fábrica administra la duración de las instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3f8b7-482">`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3f8b7-483">Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3f8b7-484">Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3f8b7-485">Crear más controladores de los necesarios puede provocar retrasos en la conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3f8b7-486">Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede ser un obstáculo a la hora de reaccionar ante los cambios de DNS.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="3f8b7-487">La duración de controlador predeterminada es dos minutos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3f8b7-488">El valor predeterminado se puede reemplazar individualmente en cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="3f8b7-489">Para ello, llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> en el `IHttpClientBuilder` que se devuelve cuando se crea el cliente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="3f8b7-490">No hace falta eliminar el cliente,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="3f8b7-491">ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3f8b7-492">`IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-493">Normalmente, las instancias de `HttpClient` pueden tratarse como objetos de .NET que no requieren eliminación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="3f8b7-494">Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-495">Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3f8b7-496">Alternativas a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3f8b7-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3f8b7-497">El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3f8b7-498">Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3f8b7-499">Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3f8b7-500">Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3f8b7-501">Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3f8b7-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3f8b7-503">Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3f8b7-504">Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3f8b7-505">`SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-506">Este uso compartido impide el agotamiento del socket.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3f8b7-507">`SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="3f8b7-508">Cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-508">Cookies</span></span>

<span data-ttu-id="3f8b7-509">Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3f8b7-510">El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3f8b7-511">En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3f8b7-512">Deshabilitación del control automático de cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3f8b7-513">Evitar `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3f8b7-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3f8b7-514">Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3f8b7-515">Registro</span><span class="sxs-lookup"><span data-stu-id="3f8b7-515">Logging</span></span>

<span data-ttu-id="3f8b7-516">Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3f8b7-517">Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="3f8b7-518">El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3f8b7-519">La categoría de registro usada en cada cliente incluye el nombre del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3f8b7-520">Así, por ejemplo, un cliente llamado *MyNamedClient* registra mensajes con una categoría `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="3f8b7-521">Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-522">En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3f8b7-523">En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3f8b7-524">El registro también se produce dentro de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-525">En nuestro caso de ejemplo de *MyNamedClient*, esos mensajes se registran en la categoría de registro `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="3f8b7-526">En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que la solicitud se envíe por la red.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="3f8b7-527">En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3f8b7-528">Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3f8b7-529">Esto puede englobar cambios, por ejemplo, en los encabezados de solicitud o en el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="3f8b7-530">Si el nombre del cliente se incluye en la categoría de registro, dicho registro se podrá filtrar para encontrar clientes con nombre específicos cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3f8b7-531">Configurar HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3f8b7-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3f8b7-532">Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3f8b7-533">Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3f8b7-534">Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3f8b7-535">Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3f8b7-536">Uso de IHttpClientFactory en una aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="3f8b7-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3f8b7-537">En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3f8b7-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="3f8b7-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3f8b7-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="3f8b7-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3f8b7-540">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-540">In the following example:</span></span>

* <span data-ttu-id="3f8b7-541"><xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3f8b7-542">`MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3f8b7-543">`HttpClient` se utiliza para recuperar una página web.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3f8b7-544">`Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="3f8b7-545">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3f8b7-545">Additional resources</span></span>

* [<span data-ttu-id="3f8b7-546">Uso de HttpClientFactory para implementar solicitudes HTTP resistentes</span><span class="sxs-lookup"><span data-stu-id="3f8b7-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3f8b7-547">Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3f8b7-548">Implementación del patrón de interruptor</span><span class="sxs-lookup"><span data-stu-id="3f8b7-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3f8b7-549">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) y [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="3f8b7-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="3f8b7-550">Se puede registrar y usar una interfaz <xref:System.Net.Http.IHttpClientFactory> para crear y configurar instancias de <xref:System.Net.Http.HttpClient> en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3f8b7-551">Esto reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-551">It offers the following benefits:</span></span>

* <span data-ttu-id="3f8b7-552">Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-553">Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3f8b7-554">y, de igual modo, registrar otro cliente predeterminado para otros fines.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="3f8b7-555">Codifica el concepto de middleware saliente a través de controladores de delegación en `HttpClient` y proporciona extensiones para middleware basado en Polly para poder sacar partido de este.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="3f8b7-556">Administra la agrupación y duración de las instancias de `HttpClientMessageHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3f8b7-557">Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3f8b7-558">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f8b7-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f8b7-559">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="3f8b7-559">Prerequisites</span></span>

<span data-ttu-id="3f8b7-560">Los proyectos para .NET Framework requieren instalar el paquete NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="3f8b7-561">Los proyectos para .NET Core y que hagan referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ya incluyen el paquete `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3f8b7-562">Patrones de consumo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-562">Consumption patterns</span></span>

<span data-ttu-id="3f8b7-563">`IHttpClientFactory` se puede usar de varias formas en una aplicación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3f8b7-564">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3f8b7-565">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3f8b7-566">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3f8b7-567">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3f8b7-568">Ninguno de ellos es rigurosamente superior a otro,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="3f8b7-569">sino que el mejor método dependerá de las restricciones particulares de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3f8b7-570">Uso básico</span><span class="sxs-lookup"><span data-stu-id="3f8b7-570">Basic usage</span></span>

<span data-ttu-id="3f8b7-571">Se puede registrar un `IHttpClientFactory` llamando al método de extensión `AddHttpClient` en `IServiceCollection`, dentro del método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3f8b7-572">Una vez registrado, el código puede aceptar una interfaz `IHttpClientFactory` en cualquier parte donde se puedan insertar servicios por medio de la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3f8b7-573">`IHttpClientFactory` se puede usar para crear una instancia de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3f8b7-574">Cuando `IHttpClientFactory` se usa de este modo, constituye una excelente manera de refactorizar una aplicación existente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="3f8b7-575">No tiene efecto alguno en la forma en que `HttpClient` se usa.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="3f8b7-576">En aquellos sitios en los que ya se hayan creado instancias de `HttpClient`, reemplace esas apariciones por una llamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3f8b7-577">Clientes con nombre</span><span class="sxs-lookup"><span data-stu-id="3f8b7-577">Named clients</span></span>

<span data-ttu-id="3f8b7-578">Si una aplicación necesita usar `HttpClient` de diversas maneras, cada una con una configuración diferente, una opción consiste en usar **clientes con nombre**.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="3f8b7-579">La configuración de un `HttpClient` con nombre se puede realizar durante la fase de registro en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3f8b7-580">En el código anterior, se llama a `AddHttpClient` usando el nombre *github*.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="3f8b7-581">Este cliente tiene aplicadas algunas configuraciones predeterminadas, a saber, la dirección base y dos encabezados necesarios para trabajar con la API de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="3f8b7-582">Cada vez que se llama a `CreateClient`, se crea otra instancia de `HttpClient` y se llama a la acción de configuración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="3f8b7-583">Para consumir un cliente con nombre, se puede pasar un parámetro de cadena a `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="3f8b7-584">Especifique el nombre del cliente que se va a crear:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3f8b7-585">En el código anterior, no es necesario especificar un nombre de host en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3f8b7-586">Basta con pasar solo la ruta de acceso, ya que se usa la dirección base configurada del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3f8b7-587">Clientes con tipo</span><span class="sxs-lookup"><span data-stu-id="3f8b7-587">Typed clients</span></span>

<span data-ttu-id="3f8b7-588">Clientes con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-588">Typed clients:</span></span>

* <span data-ttu-id="3f8b7-589">Proporcionan las mismas funciones que los clientes con nombre sin la necesidad de usar cadenas como claves.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3f8b7-590">Ofrecen ayuda relativa al compilador e IntelliSense al consumir clientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3f8b7-591">Facilitan una sola ubicación para configurar un elemento `HttpClient` determinado e interactuar con él.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3f8b7-592">Por ejemplo, el mismo cliente con tipo se puede usar para un punto de conexión back-end único y encapsular toda la lógica que se ocupa de ese punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="3f8b7-593">Funcionan con la inserción de dependencias y se pueden insertar en la aplicación cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="3f8b7-594">Un cliente con tipo acepta un parámetro `HttpClient` en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="3f8b7-595">En el código anterior, la configuración se mueve al cliente con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="3f8b7-596">El objeto `HttpClient` se expone como una propiedad pública.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="3f8b7-597">Se pueden definir métodos específicos de API que exponen la funcionalidad `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3f8b7-598">El método `GetAspNetDocsIssues` encapsula el código necesario para consultar y analizar los últimos problemas abiertos de un repositorio de GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="3f8b7-599">Para registrar un cliente con tipo, se puede usar el método de extensión genérico <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dentro de `Startup.ConfigureServices`, especificando la clase del cliente con tipo:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3f8b7-600">El cliente con tipo se registra como transitorio con inserción con dependencias,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3f8b7-601">y se puede insertar y consumir directamente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3f8b7-602">Si lo prefiere, la configuración de un cliente con nombre se puede especificar durante su registro en `Startup.ConfigureServices`, en lugar de en su constructor:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3f8b7-603">El `HttpClient` se puede encapsular completamente dentro de un cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="3f8b7-604">En lugar de exponerlo como una propiedad, se pueden proporcionar métodos públicos que llamen a la instancia de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3f8b7-605">En el código anterior, el `HttpClient` se almacena como un campo privado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="3f8b7-606">Todo el acceso para realizar llamadas externas pasa por el método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3f8b7-607">Clientes generados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-607">Generated clients</span></span>

<span data-ttu-id="3f8b7-608">`IHttpClientFactory` se puede usar en combinación con otras bibliotecas de terceros, como [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3f8b7-609">Refit es una biblioteca de REST para .NET</span><span class="sxs-lookup"><span data-stu-id="3f8b7-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3f8b7-610">que convierte las API de REST en interfaces en vivo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3f8b7-611">Se genera una implementación de la interfaz dinámicamente por medio de `RestService`, usando `HttpClient` para realizar las llamadas HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3f8b7-612">Se define una interfaz y una respuesta para representar la API externa y su correspondiente respuesta:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3f8b7-613">Un cliente con tipo se puede agregar usando Refit para generar la implementación:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3f8b7-614">La interfaz definida se puede usar cuando sea preciso con la implementación proporcionada por la inserción de dependencias y Refit:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3f8b7-615">Middleware de solicitud saliente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-615">Outgoing request middleware</span></span>

<span data-ttu-id="3f8b7-616">`HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3f8b7-617">`IHttpClientFactory` permite definir fácilmente los controladores que se usarán en cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="3f8b7-618">Admite el registro y encadenamiento de varios controladores para crear una canalización de middleware de solicitud saliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3f8b7-619">Cada uno de estos controladores es capaz de realizar la tarea antes y después de la solicitud de salida.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3f8b7-620">Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="3f8b7-621">Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="3f8b7-622">Para crear un controlador, defina una clase que se derive de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="3f8b7-623">Invalide el método `SendAsync` para ejecutar el código antes de pasar la solicitud al siguiente controlador de la canalización:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3f8b7-624">El código anterior define un controlador básico.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="3f8b7-625">Comprueba si se ha incluido un encabezado `X-API-KEY` en la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="3f8b7-626">Si no está presente, puede evitar la llamada HTTP y devolver una respuesta adecuada.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="3f8b7-627">Durante el registro, se pueden agregar uno o varios controladores a la configuración de una instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="3f8b7-628">Esta tarea se realiza a través de métodos de extensión en <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="3f8b7-629">En el código anterior, `ValidateHeaderHandler` se ha registrado con inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3f8b7-630">El controlador **debe** estar registrado en la inserción de dependencias como servicio transitorio, nunca como servicio con ámbito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="3f8b7-631">Si el controlador se registra como servicio con ámbito y se pueden eliminar los servicios de los que depende el controlador:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="3f8b7-632">Los servicios del controlador se pueden eliminar antes de que el controlador deje de estar en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="3f8b7-633">Los servicios del controlador que se eliminen provocarán un error en él.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="3f8b7-634">Una vez registrado, se puede llamar a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, con lo que se pasa el tipo de controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="3f8b7-635">Se pueden registrar varios controladores en el orden en que deben ejecutarse.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3f8b7-636">Cada controlador contiene el siguiente controlador hasta que el último `HttpClientHandler` ejecuta la solicitud:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3f8b7-637">Use uno de los siguientes enfoques para compartir el estado por solicitud con controladores de mensajes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3f8b7-638">Pase datos al controlador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="3f8b7-639">Use `IHttpContextAccessor` para acceder a la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="3f8b7-640">Cree un objeto de almacenamiento `AsyncLocal` personalizado para pasar los datos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3f8b7-641">Usar controladores basados en Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-641">Use Polly-based handlers</span></span>

<span data-ttu-id="3f8b7-642">`IHttpClientFactory` se integra con una biblioteca de terceros muy conocida denominada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3f8b7-643">Polly es una biblioteca con capacidades de resistencia y control de errores transitorios para .NET.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3f8b7-644">Permite a los desarrolladores expresar directivas como, por ejemplo, de reintento, interruptor, tiempo de espera, aislamiento compartimentado y reserva de forma fluida y segura para los subprocesos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3f8b7-645">Se proporcionan métodos de extensión para hacer posible el uso de directivas de Polly con instancias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-646">Las extensiones de Polly:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-646">The Polly extensions:</span></span>

* <span data-ttu-id="3f8b7-647">permiten agregar controladores basados en Polly a los clientes;</span><span class="sxs-lookup"><span data-stu-id="3f8b7-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="3f8b7-648">se pueden usar tras instalar el paquete NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/),</span><span class="sxs-lookup"><span data-stu-id="3f8b7-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="3f8b7-649">aunque este no está incluido en la plataforma compartida ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3f8b7-650">Control de errores transitorios</span><span class="sxs-lookup"><span data-stu-id="3f8b7-650">Handle transient faults</span></span>

<span data-ttu-id="3f8b7-651">Los errores más comunes se producen cuando las llamadas HTTP externas son transitorias.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3f8b7-652">Por ello, se incluye un método de extensión muy práctico denominado `AddTransientHttpErrorPolicy`, que permite definir una directiva para controlar los errores transitorios.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3f8b7-653">Las directivas que se configuran con este método de extensión controlan `HttpRequestException`, las respuestas HTTP 5xx y las respuestas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="3f8b7-654">La extensión `AddTransientHttpErrorPolicy` se puede usar en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3f8b7-655">La extensión da acceso a un objeto `PolicyBuilder`, configurado para controlar los errores que pueden constituir un posible error transitorio:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="3f8b7-656">En el código anterior, se define una directiva `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3f8b7-657">Las solicitudes erróneas se reintentan hasta tres veces con un retardo de 600 ms entre intentos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3f8b7-658">Seleccionar directivas dinámicamente</span><span class="sxs-lookup"><span data-stu-id="3f8b7-658">Dynamically select policies</span></span>

<span data-ttu-id="3f8b7-659">Existen más métodos de extensión que pueden servir para agregar controladores basados en Polly.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="3f8b7-660">Una de esas extensiones es `AddPolicyHandler`, que tiene varias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="3f8b7-661">Una de esas sobrecargas permite inspeccionar la solicitud al dilucidar qué directiva aplicar:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3f8b7-662">En el código anterior, si la solicitud GET saliente es del tipo HTTP, se aplica un tiempo de espera de 10 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3f8b7-663">En cualquier otro método HTTP, se usa un tiempo de espera de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3f8b7-664">Agregar varios controladores de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="3f8b7-665">Es habitual anidar directivas de Polly para proporcionar una funcionalidad mejorada:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3f8b7-666">En el ejemplo anterior, se agregan dos controladores.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="3f8b7-667">En el primer ejemplo se usa la extensión `AddTransientHttpErrorPolicy` para agregar una directiva de reintento.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="3f8b7-668">Las solicitudes con error se reintentan hasta tres veces.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="3f8b7-669">La segunda llamada a `AddTransientHttpErrorPolicy` agrega una directiva de interruptor.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="3f8b7-670">Las solicitudes externas subsiguientes se bloquean durante 30 segundos si se producen cinco intentos infructuosos seguidos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="3f8b7-671">Las directivas de interruptor tienen estado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3f8b7-672">Así, todas las llamadas realizadas a través de este cliente comparten el mismo estado de circuito.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3f8b7-673">Agregar directivas desde el Registro de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="3f8b7-674">Una forma de administrar las directivas usadas habitualmente consiste en definirlas una vez y registrarlas con `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="3f8b7-675">Se proporciona un método de extensión que permite agregar un controlador por medio de una directiva del Registro:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="3f8b7-676">En el código anterior, se registran dos directivas cuando se agrega `PolicyRegistry` a `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="3f8b7-677">Para usar una directiva del Registro, se usa el método `AddPolicyHandlerFromRegistry` pasando el nombre de la directiva que se va a aplicar.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="3f8b7-678">Encontrará más información sobre `IHttpClientFactory` y las integraciones de Polly en la [wiki de Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3f8b7-679">HttpClient y administración de la duración</span><span class="sxs-lookup"><span data-stu-id="3f8b7-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="3f8b7-680">Cada vez que se llama a `CreateClient` en `IHttpClientFactory`, se devuelve una nueva instancia de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-681">Hay un controlador <xref:System.Net.Http.HttpMessageHandler> por cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="3f8b7-682">La fábrica administra la duración de las instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3f8b7-683">`IHttpClientFactory` agrupa las instancias de `HttpMessageHandler` creadas por Factory para reducir el consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3f8b7-684">Se puede reutilizar una instancia de `HttpMessageHandler` del grupo al crear una instancia de `HttpClient` si su duración aún no ha expirado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3f8b7-685">Se recomienda agrupar controladores porque cada uno de ellos normalmente administra sus propias conexiones HTTP subyacentes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3f8b7-686">Crear más controladores de los necesarios puede provocar retrasos en la conexión.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3f8b7-687">Además, algunos controladores dejan las conexiones abiertas de forma indefinida, lo que puede ser un obstáculo a la hora de reaccionar ante los cambios de DNS.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="3f8b7-688">La duración de controlador predeterminada es dos minutos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3f8b7-689">El valor predeterminado se puede reemplazar individualmente en cada cliente con nombre.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="3f8b7-690">Para ello, llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> en el `IHttpClientBuilder` que se devuelve cuando se crea el cliente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="3f8b7-691">No hace falta eliminar el cliente,</span><span class="sxs-lookup"><span data-stu-id="3f8b7-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="3f8b7-692">ya que se cancelan las solicitudes salientes y la instancia de `HttpClient` determinada no se puede usar después de llamar a <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3f8b7-693">`IHttpClientFactory` realiza un seguimiento y elimina los recursos que usan las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-694">Normalmente, las instancias de `HttpClient` pueden tratarse como objetos de .NET que no requieren eliminación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="3f8b7-695">Mantener una sola instancia de `HttpClient` activa durante un período prolongado es un patrón común que se utiliza antes de la concepción de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3f8b7-696">Este patrón se convierte en innecesario tras la migración a `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3f8b7-697">Alternativas a IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3f8b7-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3f8b7-698">El uso de `IHttpClientFactory` en una aplicación habilitada para la inserción de dependencias evita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3f8b7-699">Problemas de agotamiento de recursos mediante la agrupación de instancias de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3f8b7-700">Problemas de DNS obsoletos al recorrer las instancias de `HttpMessageHandler` a intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3f8b7-701">Existen formas alternativas de solucionar los problemas anteriores mediante una instancia de <xref:System.Net.Http.SocketsHttpHandler> de larga duración.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3f8b7-702">Cree una instancia de `SocketsHttpHandler` al iniciar la aplicación y úsela para la vida útil de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3f8b7-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> con un valor adecuado en función de los tiempos de actualización de DNS.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3f8b7-704">Cree instancias de `HttpClient` mediante `new HttpClient(handler, disposeHandler: false)` según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3f8b7-705">Los enfoques anteriores solucionan los problemas de administración de recursos que `IHttpClientFactory` resuelve de forma similar.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3f8b7-706">`SocketsHttpHandler` comparte las conexiones entre las instancias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3f8b7-707">Este uso compartido impide el agotamiento del socket.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3f8b7-708">`SocketsHttpHandler` recorre las conexiones según `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="3f8b7-709">Cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-709">Cookies</span></span>

<span data-ttu-id="3f8b7-710">Las instancias de `HttpMessageHandler` agrupadas generan objetos `CookieContainer` que se comparten.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3f8b7-711">El uso compartido de objetos `CookieContainer` no previsto suele generar código incorrecto.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3f8b7-712">En el caso de las aplicaciones que requieren cookies, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3f8b7-713">Deshabilitación del control automático de cookies</span><span class="sxs-lookup"><span data-stu-id="3f8b7-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3f8b7-714">Evitar `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3f8b7-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3f8b7-715">Llame a <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para deshabilitar el control automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3f8b7-716">Registro</span><span class="sxs-lookup"><span data-stu-id="3f8b7-716">Logging</span></span>

<span data-ttu-id="3f8b7-717">Los clientes que se han creado a través de `IHttpClientFactory` registran mensajes de registro de todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3f8b7-718">Habilite el nivel de información adecuado en la configuración del registro para ver los mensajes de registro predeterminados.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="3f8b7-719">El registro de más información, como el registro de encabezados de solicitud, solo se incluye en el nivel de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3f8b7-720">La categoría de registro usada en cada cliente incluye el nombre del cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3f8b7-721">Así, por ejemplo, un cliente llamado *MyNamedClient* registra mensajes con una categoría `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="3f8b7-722">Los mensajes con el sufijo *LogicalHandler* se producen fuera de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-723">En la solicitud, los mensajes se registran antes de que cualquier otro controlador de la canalización haya procesado la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3f8b7-724">En la respuesta, los mensajes se registran después de que cualquier otro controlador de la canalización haya recibido la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3f8b7-725">El registro también se produce dentro de la canalización de controlador de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3f8b7-726">En nuestro caso de ejemplo de *MyNamedClient*, esos mensajes se registran en la categoría de registro `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="3f8b7-727">En la solicitud, esto tiene lugar después de que todos los demás controladores se hayan ejecutado y justo antes de que la solicitud se envíe por la red.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="3f8b7-728">En la respuesta, este registro incluye el estado de la respuesta antes de que vuelva a pasar por la canalización del controlador.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3f8b7-729">Al habilitar el registro tanto dentro como fuera de la canalización, se podrán inspeccionar los cambios realizados por otros controladores de la canalización.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3f8b7-730">Esto puede englobar cambios, por ejemplo, en los encabezados de solicitud o en el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="3f8b7-731">Si el nombre del cliente se incluye en la categoría de registro, dicho registro se podrá filtrar para encontrar clientes con nombre específicos cuando sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3f8b7-732">Configurar HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3f8b7-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3f8b7-733">Puede que sea necesario controlar la configuración del elemento `HttpMessageHandler` interno usado por un cliente.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3f8b7-734">Se devuelve un `IHttpClientBuilder` cuando se agregan clientes con nombre o con tipo.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3f8b7-735">Se puede usar el método de extensión <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para definir un delegado.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3f8b7-736">Este delegado servirá para crear y configurar el elemento principal `HttpMessageHandler` que ese cliente usa:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3f8b7-737">Uso de IHttpClientFactory en una aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="3f8b7-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3f8b7-738">En una aplicación de consola, agregue las siguientes referencias de paquete al proyecto:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3f8b7-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="3f8b7-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3f8b7-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="3f8b7-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3f8b7-741">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-741">In the following example:</span></span>

* <span data-ttu-id="3f8b7-742"><xref:System.Net.Http.IHttpClientFactory> está registrado en el contenedor de servicios del [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3f8b7-743">`MyService` crea una instancia de generador de clientes a partir del servicio, que se usa para crear un elemento `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3f8b7-744">`HttpClient` se utiliza para recuperar una página web.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3f8b7-745">`Main` crea un ámbito para ejecutar el método `GetPage` del servicio y escribe los primeros 500 caracteres del contenido de la página web en la consola.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="3f8b7-746">Middleware de propagación de encabezados</span><span class="sxs-lookup"><span data-stu-id="3f8b7-746">Header propagation middleware</span></span>

<span data-ttu-id="3f8b7-747">La propagación de encabezado es un middleware compatible con la comunidad que se usa para propagar encabezados HTTP de la solicitud entrante a las solicitudes de cliente HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="3f8b7-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="3f8b7-748">Para utilizar la propagación de encabezados:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-748">To use header propagation:</span></span>

* <span data-ttu-id="3f8b7-749">Haga referencia al puerto de comunidad admitido del paquete [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="3f8b7-750">ASP.NET Core 3.1 y las versiones posteriores admiten [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="3f8b7-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="3f8b7-751">Configure el middleware y `HttpClient` en `Startup`:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="3f8b7-752">El cliente incluye los encabezados configurados en las solicitudes salientes:</span><span class="sxs-lookup"><span data-stu-id="3f8b7-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="3f8b7-753">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3f8b7-753">Additional resources</span></span>

* [<span data-ttu-id="3f8b7-754">Uso de HttpClientFactory para implementar solicitudes HTTP resistentes</span><span class="sxs-lookup"><span data-stu-id="3f8b7-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3f8b7-755">Implementación de reintentos de llamada HTTP con retroceso exponencial con HttpClientFactory y las directivas de Polly</span><span class="sxs-lookup"><span data-stu-id="3f8b7-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3f8b7-756">Implementación del patrón de interruptor</span><span class="sxs-lookup"><span data-stu-id="3f8b7-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
