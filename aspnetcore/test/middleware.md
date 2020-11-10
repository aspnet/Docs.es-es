---
title: Prueba del middleware de ASP.NET Core
author: tratcher
description: Aprenda a probar middleware de ASP.NET Core con TestServer.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051439"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="3d75c-103">Prueba del middleware de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d75c-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="3d75c-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="3d75c-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="3d75c-105">El middleware se puede probar en aislamiento con <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="3d75c-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="3d75c-106">Eso le permite lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3d75c-106">It allows you to:</span></span>

* <span data-ttu-id="3d75c-107">Crear una instancia de la canalización de una aplicación que contenga solo los componentes que se necesiten probar.</span><span class="sxs-lookup"><span data-stu-id="3d75c-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="3d75c-108">Enviar solicitudes personalizadas para comprobar el comportamiento del middleware.</span><span class="sxs-lookup"><span data-stu-id="3d75c-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="3d75c-109">Ventajas:</span><span class="sxs-lookup"><span data-stu-id="3d75c-109">Advantages:</span></span>

* <span data-ttu-id="3d75c-110">Las solicitudes se envían en memoria en lugar de serializarse a través de la red.</span><span class="sxs-lookup"><span data-stu-id="3d75c-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="3d75c-111">Esto evita problemas adicionales, como la administración de puertos y los certificados HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3d75c-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="3d75c-112">Las excepciones del middleware pueden fluir directamente de vuelta a la prueba que llama.</span><span class="sxs-lookup"><span data-stu-id="3d75c-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="3d75c-113">Es posible personalizar estructuras de datos del servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext>, directamente en la prueba.</span><span class="sxs-lookup"><span data-stu-id="3d75c-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="3d75c-114">Configuración de TestServer</span><span class="sxs-lookup"><span data-stu-id="3d75c-114">Set up the TestServer</span></span>

<span data-ttu-id="3d75c-115">Cree una prueba en el proyecto de prueba:</span><span class="sxs-lookup"><span data-stu-id="3d75c-115">In the test project, create a test:</span></span>

* <span data-ttu-id="3d75c-116">Compile e inicie un host que use <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="3d75c-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="3d75c-117">Agregue los servicios necesarios que usa el middleware.</span><span class="sxs-lookup"><span data-stu-id="3d75c-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="3d75c-118">Agregue el paquete NuGet [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) al proyecto:</span><span class="sxs-lookup"><span data-stu-id="3d75c-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="3d75c-119">Configure la canalización de procesamiento para usar el middleware para la prueba.</span><span class="sxs-lookup"><span data-stu-id="3d75c-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="3d75c-120">Envío de solicitudes con HttpClient</span><span class="sxs-lookup"><span data-stu-id="3d75c-120">Send requests with HttpClient</span></span>

<span data-ttu-id="3d75c-121">Envíe una solicitud mediante <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="3d75c-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="3d75c-122">Declare el resultado.</span><span class="sxs-lookup"><span data-stu-id="3d75c-122">Assert the result.</span></span> <span data-ttu-id="3d75c-123">En primer lugar, realice una aserción opuesta al resultado que espera.</span><span class="sxs-lookup"><span data-stu-id="3d75c-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="3d75c-124">Una ejecución inicial con una aserción de falso positivo confirma que se produce un error en la prueba cuando el middleware funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="3d75c-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="3d75c-125">Ejecute la prueba y confirme que se produce un error en ella.</span><span class="sxs-lookup"><span data-stu-id="3d75c-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="3d75c-126">En el ejemplo siguiente, el middleware debe devolver un código de estado 404 ( *No encontrado* ) cuando se solicita el punto de conexión raíz.</span><span class="sxs-lookup"><span data-stu-id="3d75c-126">In the following example, the middleware should return a 404 status code ( *Not Found* ) when the root endpoint is requested.</span></span> <span data-ttu-id="3d75c-127">Realice la primera serie de pruebas con `Assert.NotEqual( ... );`, lo que debería generar un error:</span><span class="sxs-lookup"><span data-stu-id="3d75c-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="3d75c-128">Cambie la aserción para probar el middleware en condiciones de funcionamiento normales.</span><span class="sxs-lookup"><span data-stu-id="3d75c-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="3d75c-129">La prueba final usa `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="3d75c-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="3d75c-130">Vuelva a ejecutar la prueba para confirmar que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="3d75c-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="3d75c-131">Envío de solicitudes con HttpContext</span><span class="sxs-lookup"><span data-stu-id="3d75c-131">Send requests with HttpContext</span></span>

<span data-ttu-id="3d75c-132">Una aplicación de prueba también puede enviar una solicitud mediante [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="3d75c-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="3d75c-133">En el siguiente ejemplo, se realizan varias comprobaciones cuando el middleware procesa `https://example.com/A/Path/?and=query`:</span><span class="sxs-lookup"><span data-stu-id="3d75c-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="3d75c-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permite la configuración directa de un objeto <xref:Microsoft.AspNetCore.Http.HttpContext> en lugar de usar las abstracciones de <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3d75c-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="3d75c-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estructuras que solo están disponibles en el servidor, como [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="3d75c-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="3d75c-136">Como en el ejemplo anterior, en el que se intentaba obtener una respuesta *404 - No encontrado* , compruebe lo contrario a cada instrucción `Assert` de la prueba anterior.</span><span class="sxs-lookup"><span data-stu-id="3d75c-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="3d75c-137">La comprobación confirma que se produce un error en la prueba cuando el middleware funciona con normalidad.</span><span class="sxs-lookup"><span data-stu-id="3d75c-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="3d75c-138">Una vez que haya confirmado que la prueba de falso positivo funciona, establezca las instrucciones `Assert` finales para las condiciones y los valores esperados de la prueba.</span><span class="sxs-lookup"><span data-stu-id="3d75c-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="3d75c-139">Vuelva a ejecutarla para confirmar que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="3d75c-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="3d75c-140">Limitaciones de TestServer</span><span class="sxs-lookup"><span data-stu-id="3d75c-140">TestServer limitations</span></span>

<span data-ttu-id="3d75c-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="3d75c-141">TestServer:</span></span>

* <span data-ttu-id="3d75c-142">se creó para replicar los comportamientos del servidor a fin de probar el middleware.</span><span class="sxs-lookup"><span data-stu-id="3d75c-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="3d75c-143">\* **no** _ intenta replicar todos los comportamientos de <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3d75c-143">Does \* **not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="3d75c-144">_Trata de proporcionar al cliente acceso a tanto control sobre el servidor como sea posible y con tanta visibilidad como sea posible sobre lo que sucede en el servidor.</span><span class="sxs-lookup"><span data-stu-id="3d75c-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="3d75c-145">Por ejemplo, puede producir excepciones no iniciadas normalmente por `HttpClient` para comunicar directamente el estado del servidor.</span><span class="sxs-lookup"><span data-stu-id="3d75c-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="3d75c-146">No establece de forma predeterminada algunos encabezados específicos de transporte, ya que normalmente no son pertinentes para el middleware.</span><span class="sxs-lookup"><span data-stu-id="3d75c-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="3d75c-147">Para obtener más información, vea la siguiente sección.</span><span class="sxs-lookup"><span data-stu-id="3d75c-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="3d75c-148">Encabezados Content-Length and Transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="3d75c-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="3d75c-149">TestServer \* **no** _ establece encabezados de solicitud o respuesta relacionados con el transporte, como [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) o [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="3d75c-149">TestServer does \* **not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="3d75c-150">Las aplicaciones deben evitar depender de estos encabezados porque su uso varía según el cliente, el escenario y el protocolo.</span><span class="sxs-lookup"><span data-stu-id="3d75c-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="3d75c-151">Si `Content-Length` y `Transfer-Encoding` son necesarios para probar un escenario concreto, se pueden especificar en la prueba al crear <xref:System.Net.Http.HttpRequestMessage> o <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="3d75c-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="3d75c-152">Para más información, consulte los siguientes problemas de GitHub:</span><span class="sxs-lookup"><span data-stu-id="3d75c-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="3d75c-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="3d75c-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="3d75c-154">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="3d75c-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="3d75c-155">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="3d75c-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
