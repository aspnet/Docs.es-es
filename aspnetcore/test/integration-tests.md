---
title: Pruebas de integración en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo las pruebas de integración garantizan que los componentes de una aplicación, como la base de datos, el sistema de archivos y la red, funcionen correctamente en la infraestructura.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: f1ce6a209ef3ca85abe0a6f1ac61d85bec52d17a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050828"
---
# <a name="integration-tests-in-aspnet-core"></a>Pruebas de integración en ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) y [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Las pruebas de integración garantizan que los componentes de una aplicación funcionan correctamente en un nivel que incluye la infraestructura auxiliar de la aplicación, como la base de datos, el sistema de archivos y la red. ASP.NET Core admite las pruebas de integración mediante un marco de pruebas unitarias con un host web de prueba y un servidor de pruebas en memoria.

En este artículo se da por hecho un conocimiento básico de las pruebas unitarias. Si no está familiarizado con los conceptos de las pruebas, vea el tema [Pruebas unitaria en .NET Core y .NET Standard](/dotnet/core/testing/) y su contenido vinculado.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

La aplicación de ejemplo es una aplicación de Razor Pages que da por hecho un conocimiento básico de Razor Pages. Si no está familiarizado con Razor Pages, consulte los temas siguientes:

* [Introducción a Razor Pages](xref:razor-pages/index)
* [Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* Pruebas unitarias de [Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Para probar SPA, se recomienda una herramienta como [Selenium](https://www.seleniumhq.org/), que puede automatizar un explorador.

## <a name="introduction-to-integration-tests"></a>Introducción a las pruebas de integración

Las pruebas de integración evalúan los componentes de una aplicación en un nivel más amplio que las [pruebas unitarias](/dotnet/core/testing/). Las pruebas unitarias se usan para probar componentes de software aislados, como métodos de clase individuales. Las pruebas de integración confirman que dos o más componentes de una aplicación funcionan juntos para generar un resultado esperado, lo que posiblemente incluya a todos los componentes necesarios para procesar por completo una solicitud.

Estas pruebas más amplias se usan para probar la infraestructura de la aplicación y todo el marco, lo que a menudo incluye los siguientes componentes:

* Base de datos
* Sistema de archivos
* Dispositivos de red
* Canalización de solicitud-respuesta

Las pruebas unitarias usan componentes fabricados, conocidos como *emulaciones* u *objetos ficticios*, en lugar de componentes de las infraestructura.

A diferencia de las pruebas unitarias, las pruebas de integración:

* Usan los componentes reales que emplea la aplicación en producción.
* Necesitan más código y procesamiento de datos.
* Tardan más en ejecutarse.

Por lo tanto, limite el uso de pruebas de integración a los escenarios de infraestructura más importantes. Si un comportamiento se puede probar mediante una prueba unitaria o una prueba de integración, opte por la prueba unitaria.

> [!TIP]
> No escriba pruebas de integración para cada posible permutación de datos y acceso a archivos con bases de datos y sistemas de archivos. Independientemente de cuántas ubicaciones de una aplicación interactúen con bases de datos y sistemas de archivos, un conjunto centrado de pruebas de integración de lectura, escritura, actualización y eliminación suele ser capaz de probar adecuadamente los componentes de sistema de archivos y base de datos. Use pruebas unitarias para las pruebas rutinarias de lógica de métodos que interactúan con estos componentes. En las pruebas unitarias, el uso de simulaciones o emulaciones de infraestructura da lugar a una ejecución más rápida de esas pruebas.

> [!NOTE]
> En las conversaciones de las pruebas de integración, el proyecto probado suele denominarse *Sistema a prueba* o "SUT" para abreviar.
>
> *En este tema se usa "SUT" para referirse a la aplicación ASP.NET Core probada.*

## <a name="aspnet-core-integration-tests"></a>Pruebas de integración de ASP.NET Core

Para las pruebas de integración en ASP.NET Core se necesita lo siguiente:

* Un proyecto de prueba, que se usa para contener y ejecutar las pruebas. El proyecto de prueba tiene una referencia al SUT.
* El proyecto de prueba crea un host web de prueba para el SUT y usa un cliente de servidor de pruebas para controlar las solicitudes y las respuestas con el SUT.
* Se usa un ejecutor de pruebas para ejecutar las pruebas y notificar los resultados de estas.

Las pruebas de integración siguen una secuencia de eventos que incluye los pasos de prueba normales *Arrange*, *Act* y *Assert*:

1. Se configura el host web del SUT.
1. Se crea un cliente de servidor de pruebas para enviar solicitudes a la aplicación.
1. Se ejecuta el paso de prueba *Arrange*: la aplicación de prueba prepara una solicitud.
1. Se ejecuta el paso de prueba *Act*: el cliente envía la solicitud y recibe la respuesta.
1. Se ejecuta el paso de prueba *Assert*: la respuesta *real* se valida como *correcta* o *errónea* en función de una respuesta *esperada*.
1. El proceso continúa hasta que se ejecutan todas las pruebas.
1. Se notifican los resultados de la prueba.

Normalmente, el host web de prueba se configura de manera diferente al host web normal de la aplicación para las series de pruebas. Por ejemplo, puede usarse una base de datos diferente u otra configuración de aplicación para las pruebas.

Los componentes de infraestructura, como el host web de prueba y el servidor de pruebas en memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), se proporcionan o se administran mediante el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing). El uso de este paquete simplifica la creación y ejecución de pruebas.

El paquete `Microsoft.AspNetCore.Mvc.Testing` controla las siguientes tareas:

* Copia el archivo de dependencias ( *.deps*) del SUT en el directorio *bin* del proyecto de prueba.
* Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la raíz de proyecto del SUT de modo que se puedan encontrar archivos estáticos y páginas o vistas cuando se ejecuten las pruebas.
* Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar el arranque del SUT con `TestServer`.

En la documentación de las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) se explica cómo configurar un proyecto de prueba y un ejecutor de pruebas, además de incluirse instrucciones detalladas sobre cómo ejecutar pruebas y recomendaciones sobre cómo asignar nombres a las pruebas y las clases de prueba.

> [!NOTE]
> Al crear un proyecto de prueba para una aplicación, separe las pruebas unitarias de las pruebas de integración en proyectos diferentes. Esto ayuda a garantizar que los componentes de pruebas de infraestructura no se incluyan accidentalmente en las pruebas unitarias. La separación de las pruebas unitarias y de integración también permite controlar qué conjunto de pruebas se ejecuta.

Prácticamente no hay ninguna diferencia entre la configuración de las pruebas de aplicaciones Razor Pages y MVC. La única diferencia es cómo se asigna nombre a las pruebas. En una aplicación Razor Pages, las pruebas de puntos de conexión de página normalmente se nombran según la clase del modelo de página (por ejemplo, `IndexPageTests` para probar la integración de los componentes de la página Index). En una aplicación de MVC, las pruebas suelen organizarse por clases de controlador y denominarse según los controladores que prueban (por ejemplo, `HomeControllerTests` para probar la integración de componentes del controlador Home).

## <a name="test-app-prerequisites"></a>Requisitos previos de la aplicación de prueba

El proyecto de prueba debe:

* Hacer referencia al paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).
* Especificar el SDK web en el archivo de proyecto (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Estos requisitos previos se pueden observar en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Examine el archivo *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. La aplicación de ejemplo usa el marco de pruebas [xUnit](https://xunit.github.io/) y la biblioteca de análisis [AngleSharp](https://anglesharp.github.io/), así que la aplicación de ejemplo también hace referencia a:

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

También se usa Entity Framework Core en las pruebas. La aplicación hace referencia a:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Entorno del SUT

Si el entorno del [SUT](xref:fundamentals/environments) no está establecido, el valor predeterminado del entorno es Desarrollo.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Pruebas básicas con el valor predeterminado WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se usa para crear una instancia de [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para las pruebas de integración. `TEntryPoint` es la clase de punto de entrada del SUT, normalmente la clase `Startup`.

Las clases de prueba implementan una interfaz de *accesorio de clase* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que la clase contiene pruebas y proporcionan instancias de objeto compartidas en las pruebas de la clase.

La siguiente clase de prueba, `BasicTests`, usa `WebApplicationFactory` para arrancar el SUT y proporcionar un valor [HttpClient](/dotnet/api/system.net.http.httpclient) a un método de prueba, `Get_EndpointsReturnSuccessAndCorrectContentType`. El método comprueba si el código de estado de la respuesta es correcto (códigos de estado del rango 200-299) y si el encabezado `Content-Type` es `text/html; charset=utf-8` para varias páginas de la aplicación.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea una instancia de `HttpClient` que sigue automáticamente los redireccionamientos y controla las cookies.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

De forma predeterminada, las cookies no esenciales no se conservan entre solicitudes si está habilitada la [directiva de consentimiento de RGPD](xref:security/gdpr). Para conservar las cookies no esenciales, como las usadas por el proveedor TempData, márquelas como esenciales en las pruebas. Para obtener instrucciones sobre cómo marcar una cookie como esencial, vea [cookies esenciales](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalización de WebApplicationFactory

La configuración de host web se puede crear independientemente de las clases de prueba al heredar de `WebApplicationFactory` para crear una o más fábricas personalizadas:

1. Herede de `WebApplicationFactory` e invalide [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite la configuración de la colección de servicios con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   El método `InitializeDbForTests` realiza la propagación de la base de datos de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples). El método se describe en la sección [Ejemplo de pruebas de integración: organización de la aplicación de prueba](#test-app-organization).

   El contexto de la base de datos del SUT se registra en su método `Startup.ConfigureServices`. La devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *después* de que se ejecute el código `Startup.ConfigureServices` de la aplicación. El orden de ejecución es un cambio importante para el [host genérico](xref:fundamentals/host/generic-host) con la versión de ASP.NET Core 3.0. Para usar una base de datos diferente para las pruebas a la base de datos de la aplicación, el contexto de la base de datos de la aplicación debe reemplazarse en `builder.ConfigureServices`.

   En el caso de sistemas a prueba que todavía usan el [host de web](xref:fundamentals/host/web-host), la devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *antes* que el código `Startup.ConfigureServices` del sistema a prueba. La devolución de llamada `builder.ConfigureTestServices` de la aplicación de prueba se ejecuta *después*.

   La aplicación de ejemplo busca el descriptor de servicio del contexto de la base de datos y usa el descriptor para quitar el registro del servicio. Luego, la fábrica agrega una nueva instancia de `ApplicationDbContext` que usa una base de datos en memoria para las pruebas.

   Para conectarse a una base de datos diferente a la base de datos en memoria, cambie la llamada a `UseInMemoryDatabase` para conectar el contexto a otra base de datos. Para usar una base de datos de prueba de SQL Server:

   * Haga referencia al paquete NuGet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) del archivo de proyecto.
   * Llame a `UseSqlServer` con una cadena de conexión a la base de datos.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Use la instancia de `CustomWebApplicationFactory` personalizada en las clases de prueba. En el ejemplo siguiente se usa la fábrica en la clase `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   El cliente de la aplicación de ejemplo está configurado para evitar que `HttpClient` siga los redireccionamientos. Como se explica más adelante en la sección [Autenticación ficticia](#mock-authentication), esto permite que las pruebas comprueben el resultado de la primera respuesta de la aplicación. La primera respuesta es un redireccionamiento en muchas de estas pruebas con un encabezado `Location`.

3. Una prueba típica usa los métodos auxiliares y `HttpClient` para procesar la solicitud y la respuesta:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Cualquier solicitud POST al SUT debe satisfacer la comprobación antifalsificación que realiza automáticamente el [sistema antifalsificación de protección de datos](xref:security/data-protection/introduction) de la aplicación. Para organizar la solicitud POST de una prueba, la aplicación de prueba debe:

1. Realizar una solicitud para la página.
1. Analizar la cookie antifalsificación y solicitar el token de validación de la respuesta.
1. Realizar la solicitud POST con la cookie antifalsificación y el token de validación de la solicitud aplicados.

Los métodos de extensión auxiliares `SendAsync` (*Helpers/HttpClientExtensions.cs*) y el método auxiliar `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usan el analizador [AngleSharp](https://anglesharp.github.io/) para controlar la comprobación antifalsificación con los métodos siguientes:

* `GetDocumentAsync`: recibe [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) y devuelve una instancia de `IHtmlDocument`. `GetDocumentAsync` usa una fábrica que prepara una *respuesta virtual* basada en la instancia de `HttpResponseMessage` original. Para obtener más información, vea la [documentación de AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* Los métodos de extensión `SendAsync` de `HttpClient` componen una instancia de [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) y llaman a [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitudes al SUT. Las sobrecargas de `SendAsync` aceptan el formulario HTML (`IHtmlFormElement`) y lo siguiente:
  * Botón Enviar del formulario (`IHtmlElement`)
  * Colección de valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)
  * Botón Enviar (`IHtmlElement`) y valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) es una biblioteca de análisis de terceros que se usa con fines de demostración en este tema y en la aplicación de ejemplo. AngleSharp no es compatible con las pruebas de integración de aplicaciones ASP.NET Core ni necesario. Se pueden usar otros analizadores, como [Html Agility Pack (HAP)](https://html-agility-pack.net/). Otro enfoque consiste en escribir código para controlar el token de comprobación de solicitudes y la cookie antifalsificación del sistema antifalsificación directamente.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalización del cliente con WithWebHostBuilder

Cuando se requiere configuración adicional en un método de prueba, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea una nueva instancia de `WebApplicationFactory` con un elemento [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que se personaliza aún más mediante la configuración.

El método de prueba `Post_DeleteMessageHandler_ReturnsRedirectToRoot` de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) muestra el uso de `WithWebHostBuilder`. Esta prueba realiza una eliminación de registros en la base de datos al desencadenar un envío de formulario en el SUT.

Dado que otra prueba en la clase `IndexPageTests` realiza una operación que elimina todos los registros de la base de datos y puede ejecutarse antes que el método `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, la base de datos se vuelve a propagar en este método de prueba para asegurarse de que un registro está presente para que lo elimine el SUT. La selección del primer botón de eliminación del formulario `messages` en el SUT se simula en la solicitud al SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opciones de cliente

En la tabla siguiente se muestran los elementos [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predeterminados disponibles al crear instancias de `HttpClient`.

| Opción | Descripción | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtiene o establece si las instancias de `HttpClient` deben seguir automáticamente las respuestas de redireccionamiento. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtiene o establece la dirección base de las instancias de `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtiene o establece si las instancias de `HttpClient` deben controlar las cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtiene o establece el número máximo de respuestas de redireccionamiento que deben seguir las instancias de `HttpClient`. | 7 |

Cree la clase `WebApplicationFactoryClientOptions` y pásela al método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (los valores predeterminados se muestran en el ejemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Inserción de servicios ficticios

Los servicios se pueden invalidar en una prueba con una llamada a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) en el generador de hosts. **Para insertar servicios ficticios, el SUT debe tener una clase `Startup` con un método `Startup.ConfigureServices`.**

El SUT de ejemplo incluye un servicio con ámbito que devuelve una cita. La cita se inserta en un campo oculto de la página Index cuando se solicita esta.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Páginas/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

El siguiente marcado se genera cuando se ejecuta la aplicación SUT:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para probar el servicio y la inserción de citas en una prueba de integración, la prueba inserta un servicio ficticio en el SUT. El servicio ficticio reemplaza el elemento `QuoteService` de la aplicación por un servicio proporcionado por la aplicación de prueba, denominado `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

Se llama a `ConfigureTestServices` y se registra el servicio con ámbito:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

El marcado producido durante la ejecución de la prueba refleja el texto de la cita proporcionado por `TestQuoteService`, por lo que la aserción pasa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticación ficticia

Las pruebas de la clase `AuthTests` comprueban que un punto de conexión seguro:

* Redirige a un usuario no autenticado a la página de inicio de sesión de la aplicación.
* Devuelve el contenido de un usuario autenticado.

En el SUT, la página `/SecurePage` usa una convención [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar un elemento [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a la página. Para más información, consulte las convenciones de autorización de [Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

En la prueba `Get_SecurePageRedirectsAnUnauthenticatedUser`, [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) se establece de modo que no permita los redireccionamientos al establecer [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) en `false`:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Al no permitir que el cliente siga el redireccionamiento, se pueden realizar las siguientes comprobaciones:

* El código de estado devuelto por el SUT puede comprobarse con el resultado esperado [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), no el código de estado final después del redireccionamiento a la página de inicio de sesión, que sería [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Se comprueba el valor del encabezado `Location` en los encabezados de respuesta para confirmar que empieza con `http://localhost/Identity/Account/Login`, no la respuesta de la página de inicio de sesión final, donde el encabezado `Location` no estaría presente.

La aplicación de prueba puede simular un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> en [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para probar aspectos de autenticación y autorización. Un escenario mínimo devuelve [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Se llama a `TestAuthHandler` para autenticar a un usuario cuando el esquema de autenticación está establecido en `Test`, donde `AddAuthentication` está registrado para `ConfigureTestServices`. Es importante que el esquema de `Test` coincida con el que espera la aplicación. En caso contrario, la autenticación no funcionará.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Para obtener más información sobre `WebApplicationFactoryClientOptions`, vea la sección [Opciones de cliente](#client-options).

## <a name="set-the-environment"></a>Establecimiento del entorno

De forma predeterminada, el entorno de host y aplicación del SUT se configura para usar el entorno Desarrollo. Para invalidar el entorno del sistema a prueba al usar `IHostBuilder`:

* Establezca la variable de entorno `ASPNETCORE_ENVIRONMENT` (por ejemplo, `Staging`, `Production` u otro valor personalizado, como `Testing`).
* Invalide `CreateHostBuilder` en la aplicación de prueba para leer las variables de entorno con el prefijo `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

Si el sistema a prueba usa el host web (`IWebHostBuilder`), invalide `CreateWebHostBuilder`:

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Cómo deduce la infraestructura de prueba la ruta de acceso raíz del contenido de la aplicación

El constructor `WebApplicationFactory` deduce la ruta de acceso [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación al buscar un elemento [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) en el ensamblado que contiene las pruebas de integración con una clave igual a `System.Reflection.Assembly.FullName` del ensamblado `TEntryPoint`. En caso de que no se encuentre la clave correcta, `WebApplicationFactory` vuelve a buscar un archivo de solución ( *.sln*) y anexa el nombre de ensamblado `TEntryPoint` al directorio de la solución. El directorio raíz de la aplicación (la ruta de acceso raíz del contenido) se usa para detectar los archivos de contenido y las vistas.

## <a name="disable-shadow-copying"></a>Deshabilitación de la creación de instantáneas

La creación de instantáneas hace que las pruebas se ejecuten en un directorio diferente al de salida. Para que las pruebas funcionen correctamente, se debe deshabilitar la creación de instantáneas. La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit y deshabilita la creación de instantáneas para xUnit al incluir un archivo *xunit.runner.json* con la opción de configuración correcta. Para obtener más información, vea [Configuración de xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).

Agregue el archivo *xunit.runner.json* a la raíz del proyecto de prueba con el siguiente contenido:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Eliminación de objetos

Una vez ejecutadas las pruebas de la implementación `IClassFixture`, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) y [HttpClient](/dotnet/api/system.net.http.httpclient) se eliminan cuando xUnit quita [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si los objetos cuya instancia ha creado el desarrollador requieren eliminación, quítelos de la implementación `IClassFixture`. Para obtener más información, vea [Implementar un método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Ejemplo de pruebas de integración

La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compone de dos aplicaciones:

| Aplicación | Directorio del proyecto | Descripción |
| --- | ----------------- | ----------- |
| Aplicación de mensajes (el SUT) | *src/RazorPagesProject* | Permite a un usuario agregar, analizar mensajes y eliminarlos todos o uno solo. |
| Probar la aplicación | *tests/RazorPagesProject.Tests* | Se usa para probar la integración del SUT. |

Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](https://visualstudio.microsoft.com). Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema en el directorio *tests/RazorPagesProject.Tests*:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organización de aplicación de mensajes (SUT)

El sistema a prueba es un sistema de mensajes de Razor Pages con las siguientes características:

* La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, la eliminación y el análisis de mensajes (promedio de palabras por mensaje).
* La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje). Se necesita la propiedad `Text`, que está limitada a 200 caracteres.
* Los mensajes se almacenan mediante [Base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* La aplicación contiene una capa de acceso a datos (DAL) en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*).
* Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes.
* La aplicación incluye un elemento `/SecurePage` al que solo puede acceder un usuario autenticado.

&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria para las pruebas con MSTest. En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/). Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.

Aunque la aplicación no usa el patrón del repositorio y no es un buen ejemplo del [patrón de unidad de trabajo (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo. Para obtener más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y [Lógica del controlador de pruebas](../mvc/controllers/testing.md) (el ejemplo implementa el patrón del repositorio).

### <a name="test-app-organization"></a>Organización de la aplicación de prueba

La aplicación de prueba es una aplicación de consola dentro del directorio *tests/RazorPagesProject.Tests*.

| Directorio de la aplicación de prueba | Descripción |
| ------------------ | ----------- |
| *AuthTests* | Contiene métodos de prueba para:<ul><li>Acceder a una página segura mediante un usuario no autenticado.</li><li>Acceder a una página segura mediante un usuario autenticado con una simulación <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Obtener un perfil de usuario de GitHub y comprobar el inicio de sesión de usuario del perfil.</li></ul> |
| *BasicTests* | Contiene un método de prueba para el enrutamiento y el tipo de contenido. |
| *IntegrationTests* | Contiene las pruebas de integración de la página Index que usa la clase `WebApplicationFactory` personalizada. |
| *Asistentes o utilidades* | <ul><li>*Utilities.cs* contiene el método `InitializeDbForTests` que se usa para propagar la base de datos con datos de prueba.</li><li>*HtmlHelpers.cs* proporciona un método para devolver un elemento `IHtmlDocument` de AngleSharp para que lo usen los métodos de prueba.</li><li>*HttpClientExtensions.cs* proporciona sobrecargas para que `SendAsync` envíe solicitudes al SUT.</li></ul> |

El marco de pruebas es [xUnit](https://xunit.github.io/). Las pruebas de integración se llevan a cabo con [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que incluye [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Dado que el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se usa para configurar el host de prueba y el servidor de pruebas, los paquetes `TestHost` y `TestServer` no requieren referencias de paquete directas en el archivo de proyecto o la configuración de desarrollador de la aplicación de prueba en la aplicación de prueba.

**Propagación de la base de datos para pruebas**

Las pruebas de integración suelen requerir un pequeño conjunto de datos en la base de datos antes de la ejecución de la prueba. Por ejemplo, una prueba de eliminación consiste en la eliminación de un registro de base de datos, por lo que la base de datos debe tener al menos un registro para que la solicitud de eliminación se realice correctamente.

La aplicación de ejemplo propaga la base de datos con tres mensajes en *Utilities.cs* que las pruebas pueden usar al ejecutarse:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

El contexto de la base de datos del SUT se registra en su método `Startup.ConfigureServices`. La devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *después* de que se ejecute el código `Startup.ConfigureServices` de la aplicación. Para usar otra base de datos para las pruebas, el contexto de la base de datos de la aplicación debe reemplazarse en `builder.ConfigureServices`. Para obtener más información, vea la sección [Personalización de WebApplicationFactory](#customize-webapplicationfactory).

En el caso de sistemas a prueba que todavía usan el [host de web](xref:fundamentals/host/web-host), la devolución de llamada `builder.ConfigureServices` de la aplicación de prueba se ejecuta *antes* que el código `Startup.ConfigureServices` del sistema a prueba. La devolución de llamada `builder.ConfigureTestServices` de la aplicación de prueba se ejecuta *después*.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Las pruebas de integración garantizan que los componentes de una aplicación funcionan correctamente en un nivel que incluye la infraestructura auxiliar de la aplicación, como la base de datos, el sistema de archivos y la red. ASP.NET Core admite las pruebas de integración mediante un marco de pruebas unitarias con un host web de prueba y un servidor de pruebas en memoria.

En este artículo se da por hecho un conocimiento básico de las pruebas unitarias. Si no está familiarizado con los conceptos de las pruebas, vea el tema [Pruebas unitaria en .NET Core y .NET Standard](/dotnet/core/testing/) y su contenido vinculado.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

La aplicación de ejemplo es una aplicación de Razor Pages que da por hecho un conocimiento básico de Razor Pages. Si no está familiarizado con Razor Pages, consulte los temas siguientes:

* [Introducción a Razor Pages](xref:razor-pages/index)
* [Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* Pruebas unitarias de [Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Para probar SPA, se recomienda una herramienta como [Selenium](https://www.seleniumhq.org/), que puede automatizar un explorador.

## <a name="introduction-to-integration-tests"></a>Introducción a las pruebas de integración

Las pruebas de integración evalúan los componentes de una aplicación en un nivel más amplio que las [pruebas unitarias](/dotnet/core/testing/). Las pruebas unitarias se usan para probar componentes de software aislados, como métodos de clase individuales. Las pruebas de integración confirman que dos o más componentes de una aplicación funcionan juntos para generar un resultado esperado, lo que posiblemente incluya a todos los componentes necesarios para procesar por completo una solicitud.

Estas pruebas más amplias se usan para probar la infraestructura de la aplicación y todo el marco, lo que a menudo incluye los siguientes componentes:

* Base de datos
* Sistema de archivos
* Dispositivos de red
* Canalización de solicitud-respuesta

Las pruebas unitarias usan componentes fabricados, conocidos como *emulaciones* u *objetos ficticios*, en lugar de componentes de las infraestructura.

A diferencia de las pruebas unitarias, las pruebas de integración:

* Usan los componentes reales que emplea la aplicación en producción.
* Necesitan más código y procesamiento de datos.
* Tardan más en ejecutarse.

Por lo tanto, limite el uso de pruebas de integración a los escenarios de infraestructura más importantes. Si un comportamiento se puede probar mediante una prueba unitaria o una prueba de integración, opte por la prueba unitaria.

> [!TIP]
> No escriba pruebas de integración para cada posible permutación de datos y acceso a archivos con bases de datos y sistemas de archivos. Independientemente de cuántas ubicaciones de una aplicación interactúen con bases de datos y sistemas de archivos, un conjunto centrado de pruebas de integración de lectura, escritura, actualización y eliminación suele ser capaz de probar adecuadamente los componentes de sistema de archivos y base de datos. Use pruebas unitarias para las pruebas rutinarias de lógica de métodos que interactúan con estos componentes. En las pruebas unitarias, el uso de simulaciones o emulaciones de infraestructura da lugar a una ejecución más rápida de esas pruebas.

> [!NOTE]
> En las conversaciones de las pruebas de integración, el proyecto probado suele denominarse *Sistema a prueba* o "SUT" para abreviar.
>
> *En este tema se usa "SUT" para referirse a la aplicación ASP.NET Core probada.*

## <a name="aspnet-core-integration-tests"></a>Pruebas de integración de ASP.NET Core

Para las pruebas de integración en ASP.NET Core se necesita lo siguiente:

* Un proyecto de prueba, que se usa para contener y ejecutar las pruebas. El proyecto de prueba tiene una referencia al SUT.
* El proyecto de prueba crea un host web de prueba para el SUT y usa un cliente de servidor de pruebas para controlar las solicitudes y las respuestas con el SUT.
* Se usa un ejecutor de pruebas para ejecutar las pruebas y notificar los resultados de estas.

Las pruebas de integración siguen una secuencia de eventos que incluye los pasos de prueba normales *Arrange*, *Act* y *Assert*:

1. Se configura el host web del SUT.
1. Se crea un cliente de servidor de pruebas para enviar solicitudes a la aplicación.
1. Se ejecuta el paso de prueba *Arrange*: la aplicación de prueba prepara una solicitud.
1. Se ejecuta el paso de prueba *Act*: el cliente envía la solicitud y recibe la respuesta.
1. Se ejecuta el paso de prueba *Assert*: la respuesta *real* se valida como *correcta* o *errónea* en función de una respuesta *esperada*.
1. El proceso continúa hasta que se ejecutan todas las pruebas.
1. Se notifican los resultados de la prueba.

Normalmente, el host web de prueba se configura de manera diferente al host web normal de la aplicación para las series de pruebas. Por ejemplo, puede usarse una base de datos diferente u otra configuración de aplicación para las pruebas.

Los componentes de infraestructura, como el host web de prueba y el servidor de pruebas en memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), se proporcionan o se administran mediante el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing). El uso de este paquete simplifica la creación y ejecución de pruebas.

El paquete `Microsoft.AspNetCore.Mvc.Testing` controla las siguientes tareas:

* Copia el archivo de dependencias ( *.deps*) del SUT en el directorio *bin* del proyecto de prueba.
* Establece la [raíz de contenido](xref:fundamentals/index#content-root) en la raíz de proyecto del SUT de modo que se puedan encontrar archivos estáticos y páginas o vistas cuando se ejecuten las pruebas.
* Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar el arranque del SUT con `TestServer`.

En la documentación de las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) se explica cómo configurar un proyecto de prueba y un ejecutor de pruebas, además de incluirse instrucciones detalladas sobre cómo ejecutar pruebas y recomendaciones sobre cómo asignar nombres a las pruebas y las clases de prueba.

> [!NOTE]
> Al crear un proyecto de prueba para una aplicación, separe las pruebas unitarias de las pruebas de integración en proyectos diferentes. Esto ayuda a garantizar que los componentes de pruebas de infraestructura no se incluyan accidentalmente en las pruebas unitarias. La separación de las pruebas unitarias y de integración también permite controlar qué conjunto de pruebas se ejecuta.

Prácticamente no hay ninguna diferencia entre la configuración de las pruebas de aplicaciones Razor Pages y MVC. La única diferencia es cómo se asigna nombre a las pruebas. En una aplicación Razor Pages, las pruebas de puntos de conexión de página normalmente se nombran según la clase del modelo de página (por ejemplo, `IndexPageTests` para probar la integración de los componentes de la página Index). En una aplicación de MVC, las pruebas suelen organizarse por clases de controlador y denominarse según los controladores que prueban (por ejemplo, `HomeControllerTests` para probar la integración de componentes del controlador Home).

## <a name="test-app-prerequisites"></a>Requisitos previos de la aplicación de prueba

El proyecto de prueba debe:

* Haga referencia a los siguientes paquetes:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Especificar el SDK web en el archivo de proyecto (`<Project Sdk="Microsoft.NET.Sdk.Web">`). El SDK web es necesario cuando se hace referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Estos requisitos previos se pueden observar en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Examine el archivo *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. La aplicación de ejemplo usa el marco de pruebas [xUnit](https://xunit.github.io/) y la biblioteca de análisis [AngleSharp](https://anglesharp.github.io/), así que la aplicación de ejemplo también hace referencia a:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Entorno del SUT

Si el entorno del [SUT](xref:fundamentals/environments) no está establecido, el valor predeterminado del entorno es Desarrollo.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Pruebas básicas con el valor predeterminado WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se usa para crear una instancia de [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para las pruebas de integración. `TEntryPoint` es la clase de punto de entrada del SUT, normalmente la clase `Startup`.

Las clases de prueba implementan una interfaz de *accesorio de clase* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que la clase contiene pruebas y proporcionan instancias de objeto compartidas en las pruebas de la clase.

La siguiente clase de prueba, `BasicTests`, usa `WebApplicationFactory` para arrancar el SUT y proporcionar un valor [HttpClient](/dotnet/api/system.net.http.httpclient) a un método de prueba, `Get_EndpointsReturnSuccessAndCorrectContentType`. El método comprueba si el código de estado de la respuesta es correcto (códigos de estado del rango 200-299) y si el encabezado `Content-Type` es `text/html; charset=utf-8` para varias páginas de la aplicación.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea una instancia de `HttpClient` que sigue automáticamente los redireccionamientos y controla las cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

De forma predeterminada, las cookies no esenciales no se conservan entre solicitudes si está habilitada la [directiva de consentimiento de RGPD](xref:security/gdpr). Para conservar las cookies no esenciales, como las usadas por el proveedor TempData, márquelas como esenciales en las pruebas. Para obtener instrucciones sobre cómo marcar una cookie como esencial, vea [cookies esenciales](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalización de WebApplicationFactory

La configuración de host web se puede crear independientemente de las clases de prueba al heredar de `WebApplicationFactory` para crear una o más fábricas personalizadas:

1. Herede de `WebApplicationFactory` e invalide [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite la configuración de la colección de servicios con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), que se ejecuta antes del elemento `Startup.ConfigureServices` de la aplicación. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite reemplazar y modificar los servicios registrados en la colección de servicios con [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   El método `InitializeDbForTests` realiza la propagación de la base de datos de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples). El método se describe en la sección [Ejemplo de pruebas de integración: organización de la aplicación de prueba](#test-app-organization).

2. Use la instancia de `CustomWebApplicationFactory` personalizada en las clases de prueba. En el ejemplo siguiente se usa la fábrica en la clase `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   El cliente de la aplicación de ejemplo está configurado para evitar que `HttpClient` siga los redireccionamientos. Como se explica más adelante en la sección [Autenticación ficticia](#mock-authentication), esto permite que las pruebas comprueben el resultado de la primera respuesta de la aplicación. La primera respuesta es un redireccionamiento en muchas de estas pruebas con un encabezado `Location`.

3. Una prueba típica usa los métodos auxiliares y `HttpClient` para procesar la solicitud y la respuesta:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Cualquier solicitud POST al SUT debe satisfacer la comprobación antifalsificación que realiza automáticamente el [sistema antifalsificación de protección de datos](xref:security/data-protection/introduction) de la aplicación. Para organizar la solicitud POST de una prueba, la aplicación de prueba debe:

1. Realizar una solicitud para la página.
1. Analizar la cookie antifalsificación y solicitar el token de validación de la respuesta.
1. Realizar la solicitud POST con la cookie antifalsificación y el token de validación de la solicitud aplicados.

Los métodos de extensión auxiliares `SendAsync` (*Helpers/HttpClientExtensions.cs*) y el método auxiliar `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usan el analizador [AngleSharp](https://anglesharp.github.io/) para controlar la comprobación antifalsificación con los métodos siguientes:

* `GetDocumentAsync`: recibe [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) y devuelve una instancia de `IHtmlDocument`. `GetDocumentAsync` usa una fábrica que prepara una *respuesta virtual* basada en la instancia de `HttpResponseMessage` original. Para obtener más información, vea la [documentación de AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* Los métodos de extensión `SendAsync` de `HttpClient` componen una instancia de [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) y llaman a [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitudes al SUT. Las sobrecargas de `SendAsync` aceptan el formulario HTML (`IHtmlFormElement`) y lo siguiente:
  * Botón Enviar del formulario (`IHtmlElement`)
  * Colección de valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)
  * Botón Enviar (`IHtmlElement`) y valores del formulario (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) es una biblioteca de análisis de terceros que se usa con fines de demostración en este tema y en la aplicación de ejemplo. AngleSharp no es compatible con las pruebas de integración de aplicaciones ASP.NET Core ni necesario. Se pueden usar otros analizadores, como [Html Agility Pack (HAP)](https://html-agility-pack.net/). Otro enfoque consiste en escribir código para controlar el token de comprobación de solicitudes y la cookie antifalsificación del sistema antifalsificación directamente.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalización del cliente con WithWebHostBuilder

Cuando se requiere configuración adicional en un método de prueba, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea una nueva instancia de `WebApplicationFactory` con un elemento [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que se personaliza aún más mediante la configuración.

El método de prueba `Post_DeleteMessageHandler_ReturnsRedirectToRoot` de la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) muestra el uso de `WithWebHostBuilder`. Esta prueba realiza una eliminación de registros en la base de datos al desencadenar un envío de formulario en el SUT.

Dado que otra prueba en la clase `IndexPageTests` realiza una operación que elimina todos los registros de la base de datos y puede ejecutarse antes que el método `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, la base de datos se vuelve a propagar en este método de prueba para asegurarse de que un registro está presente para que lo elimine el SUT. La selección del primer botón de eliminación del formulario `messages` en el SUT se simula en la solicitud al SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opciones de cliente

En la tabla siguiente se muestran los elementos [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predeterminados disponibles al crear instancias de `HttpClient`.

| Opción | Descripción | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtiene o establece si las instancias de `HttpClient` deben seguir automáticamente las respuestas de redireccionamiento. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtiene o establece la dirección base de las instancias de `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtiene o establece si las instancias de `HttpClient` deben controlar las cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtiene o establece el número máximo de respuestas de redireccionamiento que deben seguir las instancias de `HttpClient`. | 7 |

Cree la clase `WebApplicationFactoryClientOptions` y pásela al método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (los valores predeterminados se muestran en el ejemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Inserción de servicios ficticios

Los servicios se pueden invalidar en una prueba con una llamada a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) en el generador de hosts. **Para insertar servicios ficticios, el SUT debe tener una clase `Startup` con un método `Startup.ConfigureServices`.**

El SUT de ejemplo incluye un servicio con ámbito que devuelve una cita. La cita se inserta en un campo oculto de la página Index cuando se solicita esta.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Páginas/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

El siguiente marcado se genera cuando se ejecuta la aplicación SUT:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para probar el servicio y la inserción de citas en una prueba de integración, la prueba inserta un servicio ficticio en el SUT. El servicio ficticio reemplaza el elemento `QuoteService` de la aplicación por un servicio proporcionado por la aplicación de prueba, denominado `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

Se llama a `ConfigureTestServices` y se registra el servicio con ámbito:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

El marcado producido durante la ejecución de la prueba refleja el texto de la cita proporcionado por `TestQuoteService`, por lo que la aserción pasa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticación ficticia

Las pruebas de la clase `AuthTests` comprueban que un punto de conexión seguro:

* Redirige a un usuario no autenticado a la página de inicio de sesión de la aplicación.
* Devuelve el contenido de un usuario autenticado.

En el SUT, la página `/SecurePage` usa una convención [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar un elemento [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a la página. Para más información, consulte las convenciones de autorización de [Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

En la prueba `Get_SecurePageRedirectsAnUnauthenticatedUser`, [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) se establece de modo que no permita los redireccionamientos al establecer [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) en `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Al no permitir que el cliente siga el redireccionamiento, se pueden realizar las siguientes comprobaciones:

* El código de estado devuelto por el SUT puede comprobarse con el resultado esperado [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), no el código de estado final después del redireccionamiento a la página de inicio de sesión, que sería [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Se comprueba el valor del encabezado `Location` en los encabezados de respuesta para confirmar que empieza con `http://localhost/Identity/Account/Login`, no la respuesta de la página de inicio de sesión final, donde el encabezado `Location` no estaría presente.

La aplicación de prueba puede simular un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> en [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para probar aspectos de autenticación y autorización. Un escenario mínimo devuelve [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Se llama a `TestAuthHandler` para autenticar a un usuario cuando el esquema de autenticación está establecido en `Test`, donde `AddAuthentication` está registrado para `ConfigureTestServices`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Para obtener más información sobre `WebApplicationFactoryClientOptions`, vea la sección [Opciones de cliente](#client-options).

## <a name="set-the-environment"></a>Establecimiento del entorno

De forma predeterminada, el entorno de host y aplicación del SUT se configura para usar el entorno Desarrollo. Para invalidar el entorno del SUT:

* Establezca la variable de entorno `ASPNETCORE_ENVIRONMENT` (por ejemplo, `Staging`, `Production` u otro valor personalizado, como `Testing`).
* Reemplace `CreateWebHostBuilder` en la aplicación de prueba para leer la variable de entorno `ASPNETCORE_ENVIRONMENT`.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

El entorno también puede establecerse directamente en el generador de hosts en un valor de <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> personalizado:

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Cómo deduce la infraestructura de prueba la ruta de acceso raíz del contenido de la aplicación

El constructor `WebApplicationFactory` deduce la ruta de acceso [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación al buscar un elemento [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) en el ensamblado que contiene las pruebas de integración con una clave igual a `System.Reflection.Assembly.FullName` del ensamblado `TEntryPoint`. En caso de que no se encuentre la clave correcta, `WebApplicationFactory` vuelve a buscar un archivo de solución ( *.sln*) y anexa el nombre de ensamblado `TEntryPoint` al directorio de la solución. El directorio raíz de la aplicación (la ruta de acceso raíz del contenido) se usa para detectar los archivos de contenido y las vistas.

## <a name="disable-shadow-copying"></a>Deshabilitación de la creación de instantáneas

La creación de instantáneas hace que las pruebas se ejecuten en un directorio diferente al de salida. Para que las pruebas funcionen correctamente, se debe deshabilitar la creación de instantáneas. La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit y deshabilita la creación de instantáneas para xUnit al incluir un archivo *xunit.runner.json* con la opción de configuración correcta. Para obtener más información, vea [Configuración de xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).

Agregue el archivo *xunit.runner.json* a la raíz del proyecto de prueba con el siguiente contenido:

```json
{
  "shadowCopy": false
}
```

Si usa Visual Studio, establezca la propiedad **Copiar en el directorio de salida** del archivo en **Copiar siempre**. Si no usa Visual Studio, agregue un destino `Content` al archivo de proyecto de la aplicación de prueba:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Eliminación de objetos

Una vez ejecutadas las pruebas de la implementación `IClassFixture`, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) y [HttpClient](/dotnet/api/system.net.http.httpclient) se eliminan cuando xUnit quita [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si los objetos cuya instancia ha creado el desarrollador requieren eliminación, quítelos de la implementación `IClassFixture`. Para obtener más información, vea [Implementar un método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Ejemplo de pruebas de integración

La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compone de dos aplicaciones:

| Aplicación | Directorio del proyecto | Descripción |
| --- | ----------------- | ----------- |
| Aplicación de mensajes (el SUT) | *src/RazorPagesProject* | Permite a un usuario agregar, analizar mensajes y eliminarlos todos o uno solo. |
| Probar la aplicación | *tests/RazorPagesProject.Tests* | Se usa para probar la integración del SUT. |

Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](https://visualstudio.microsoft.com). Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema en el directorio *tests/RazorPagesProject.Tests*:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organización de aplicación de mensajes (SUT)

El sistema a prueba es un sistema de mensajes de Razor Pages con las siguientes características:

* La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, la eliminación y el análisis de mensajes (promedio de palabras por mensaje).
* La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje). Se necesita la propiedad `Text`, que está limitada a 200 caracteres.
* Los mensajes se almacenan mediante [Base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* La aplicación contiene una capa de acceso a datos (DAL) en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*).
* Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes.
* La aplicación incluye un elemento `/SecurePage` al que solo puede acceder un usuario autenticado.

&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria para las pruebas con MSTest. En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/). Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.

Aunque la aplicación no usa el patrón del repositorio y no es un buen ejemplo del [patrón de unidad de trabajo (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo. Para obtener más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y [Lógica del controlador de pruebas](../mvc/controllers/testing.md) (el ejemplo implementa el patrón del repositorio).

### <a name="test-app-organization"></a>Organización de la aplicación de prueba

La aplicación de prueba es una aplicación de consola dentro del directorio *tests/RazorPagesProject.Tests*.

| Directorio de la aplicación de prueba | Descripción |
| ------------------ | ----------- |
| *AuthTests* | Contiene métodos de prueba para:<ul><li>Acceder a una página segura mediante un usuario no autenticado.</li><li>Acceder a una página segura mediante un usuario autenticado con una simulación <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Obtener un perfil de usuario de GitHub y comprobar el inicio de sesión de usuario del perfil.</li></ul> |
| *BasicTests* | Contiene un método de prueba para el enrutamiento y el tipo de contenido. |
| *IntegrationTests* | Contiene las pruebas de integración de la página Index que usa la clase `WebApplicationFactory` personalizada. |
| *Asistentes o utilidades* | <ul><li>*Utilities.cs* contiene el método `InitializeDbForTests` que se usa para propagar la base de datos con datos de prueba.</li><li>*HtmlHelpers.cs* proporciona un método para devolver un elemento `IHtmlDocument` de AngleSharp para que lo usen los métodos de prueba.</li><li>*HttpClientExtensions.cs* proporciona sobrecargas para que `SendAsync` envíe solicitudes al SUT.</li></ul> |

El marco de pruebas es [xUnit](https://xunit.github.io/). Las pruebas de integración se llevan a cabo con [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que incluye [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Dado que el paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se usa para configurar el host de prueba y el servidor de pruebas, los paquetes `TestHost` y `TestServer` no requieren referencias de paquete directas en el archivo de proyecto o la configuración de desarrollador de la aplicación de prueba en la aplicación de prueba.

**Propagación de la base de datos para pruebas**

Las pruebas de integración suelen requerir un pequeño conjunto de datos en la base de datos antes de la ejecución de la prueba. Por ejemplo, una prueba de eliminación consiste en la eliminación de un registro de base de datos, por lo que la base de datos debe tener al menos un registro para que la solicitud de eliminación se realice correctamente.

La aplicación de ejemplo propaga la base de datos con tres mensajes en *Utilities.cs* que las pruebas pueden usar al ejecutarse:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>