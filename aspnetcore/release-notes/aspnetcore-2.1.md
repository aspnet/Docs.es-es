---
title: Novedades de ASP.NET Core 2.1
author: isaac2004
description: Obtenga información sobre las nuevas características de ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: aspnetcore-2.1
ms.openlocfilehash: 62fc9d866adcf05ff024501db68cce8bb8b11a98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059720"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="9c8ad-103">Novedades de ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="9c8ad-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="9c8ad-104">En este artículo se resaltan los cambios más importantes de ASP.NET Core 2.1, con vínculos a la documentación pertinente.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="9c8ad-105">SignalR se ha reescrito para ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="9c8ad-106">ASP.NET Core SignalR incluye una serie de mejoras:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="9c8ad-107">Un modelo de escalabilidad horizontal simplificado.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="9c8ad-108">Un nuevo cliente de JavaScript sin dependencias de jQuery.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="9c8ad-109">Un nuevo protocolo binario compacto basado en MessagePack.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="9c8ad-110">Compatibilidad con protocolos personalizados.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-110">Support for custom protocols.</span></span>
* <span data-ttu-id="9c8ad-111">Un nuevo modelo de respuesta de streaming.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-111">A new streaming response model.</span></span>
* <span data-ttu-id="9c8ad-112">Compatibilidad con clientes basados en WebSockets vacíos.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="9c8ad-113">Para más información, consulte [ASP.NET CoreSignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="no-locrazor-class-libraries"></a><span data-ttu-id="9c8ad-114">Bibliotecas de clases de Razor</span><span class="sxs-lookup"><span data-stu-id="9c8ad-114">Razor class libraries</span></span>

<span data-ttu-id="9c8ad-115">Con ASP.NET Core 2.1 es más fácil compilar e incluir una interfaz de usuario basada en Razor en una biblioteca y compartirla entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="9c8ad-116">El nuevo SDK de Razor permite compilar archivos de Razor en un proyecto de biblioteca de clases que se puede empaquetar en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="9c8ad-117">Las vistas y las páginas en las bibliotecas se detectan automáticamente y se pueden reemplazar por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="9c8ad-118">Al integrar la compilación de Razor en la compilación:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="9c8ad-119">El tiempo de inicio de la aplicación es mucho más rápido.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="9c8ad-120">Sigue habiendo actualizaciones rápidas de páginas y vistas de Razor en tiempo de ejecución como parte de un flujo de trabajo de desarrollo iterativo.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="9c8ad-121">Para obtener más información, vea [Creación de una interfaz de usuario reutilizable con el proyecto de biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="no-locidentity-ui-library--scaffolding"></a><span data-ttu-id="9c8ad-122">Biblioteca de interfaz de usuario de Identity y scaffolding</span><span class="sxs-lookup"><span data-stu-id="9c8ad-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="9c8ad-123">ASP.NET Core 2.1 proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9c8ad-124">Las aplicaciones que incluyen Identity pueden aplicar el nuevo proveedor de scaffolding de Identity para agregar de forma selectiva el código fuente incluido en la biblioteca de clases de Razor (RCL) de Identity.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="9c8ad-125">Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento;</span><span class="sxs-lookup"><span data-stu-id="9c8ad-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9c8ad-126">así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9c8ad-127">El código generado tiene prioridad sobre el mismo código de la RCL de Identity.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="9c8ad-128">Las aplicaciones que **no** incluyen autenticación pueden aplicar el proveedor de scaffolding de Identity para agregar el paquete de Identity de la RCL.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="9c8ad-129">Tiene la opción de seleccionar el código de Identity que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="9c8ad-130">Para obtener más información, vea [Scaffolding Identity en proyectos de ASP.NET Core](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="9c8ad-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9c8ad-131">HTTPS</span></span>

<span data-ttu-id="9c8ad-132">En un momento en que la seguridad y la privacidad tienen cada vez más relevancia, es importante habilitar HTTPS en las aplicaciones web.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="9c8ad-133">El cumplimiento de HTTPS es cada vez más estricto en Internet.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="9c8ad-134">Los sitios que no usan HTTPS se consideran inseguros.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="9c8ad-135">Los exploradores (Chrome, Mozilla) están empezando a exigir el uso de las características web dentro de un contexto seguro.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="9c8ad-136">El [RGPD](xref:security/gdpr) exige el uso de HTTPS para proteger la privacidad de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="9c8ad-137">Usar HTTPS en la fase de producción es esencial, pero hacerlo también en la fase de desarrollo puede ayudar a evitar problemas en la implementación (por ejemplo, vínculos inseguros).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="9c8ad-138">ASP.NET Core 2.1 incluye diversas mejoras que hacen que sea más fácil usar HTTPS en el desarrollo y configurar el protocolo HTTPS en la producción.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="9c8ad-139">Para más información, vea [Aplicación de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="9c8ad-140">Activado de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="9c8ad-140">On by default</span></span>

<span data-ttu-id="9c8ad-141">A fin de hacer posible un desarrollo de sitios web seguro, ahora HTTPS está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="9c8ad-142">A partir de 2.1, Kestrel escucha en `https://localhost:5001` cuando hay presente un certificado de desarrollo local.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="9c8ad-143">Un certificado de desarrollo se crea:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-143">A development certificate is created:</span></span>

* <span data-ttu-id="9c8ad-144">Como parte de la experiencia de primera ejecución del SDK de .NET Core, cuando el SDK se usa por primera vez.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="9c8ad-145">Manualmente, por medio de la nueva herramienta `dev-certs`.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="9c8ad-146">Ejecute `dotnet dev-certs https --trust` para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="9c8ad-147">Cumplimiento y redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="9c8ad-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="9c8ad-148">Normalmente, las aplicaciones web necesitan escuchar en HTTP y HTTPS, si bien luego redirigen todo el tráfico HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="9c8ad-149">En 2.1 se ha incluido un middleware especializado de redireccionamiento de HTTPS que redirige de forma inteligente según la presencia de puertos de servidor enlazado o configuración.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="9c8ad-150">El uso de HTTPS puede exigir aún más por medio del [protocolo de Seguridad de transporte estricta de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="9c8ad-151">HSTS indica a los exploradores que tengan acceso al sitio siempre a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="9c8ad-152">ASP.NET Core 2.1 agrega middleware de HSTS que contempla opciones de antigüedad máxima, subdominios y la lista de carga previa de HSTS.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="9c8ad-153">Configuración para producción</span><span class="sxs-lookup"><span data-stu-id="9c8ad-153">Configuration for production</span></span>

<span data-ttu-id="9c8ad-154">En un entorno de producción, HTTPS se debe configurar explícitamente.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9c8ad-155">En 2.1, se ha agregado un esquema de configuración predeterminado para configurar HTTPS para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="9c8ad-156">Las aplicaciones se pueden configurar para usar:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="9c8ad-157">Varios puntos de conexión (direcciones URL incluidas).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="9c8ad-158">Para más información, consulte [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (Kestrel: configuración de los puntos de conexión).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="9c8ad-159">El certificado que se va a usar para HTTPS desde un archivo en disco o desde un almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="9c8ad-160">RGPD</span><span class="sxs-lookup"><span data-stu-id="9c8ad-160">GDPR</span></span>

<span data-ttu-id="9c8ad-161">ASP.NET Core proporciona API y plantillas para cumplir algunos de los requisitos del [Reglamento general de protección de datos (RGPD) de la UE](https://www.eugdpr.org/).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="9c8ad-162">Para más información, vea [GDPR support in ASP.NET Core](xref:security/gdpr) (Compatibilidad con el Reglamento general de protección de datos en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="9c8ad-163">Con las [aplicaciones de muestra](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) se muestra cómo usar y probar la mayor parte de las API y los puntos de extensión del RGPD que se han agregado a las plantillas de ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="9c8ad-164">Pruebas de integración</span><span class="sxs-lookup"><span data-stu-id="9c8ad-164">Integration tests</span></span>

<span data-ttu-id="9c8ad-165">Se ha incorporado un nuevo paquete que optimiza las tareas de creación y ejecución de pruebas.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="9c8ad-166">El paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) se encarga de estas tareas:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="9c8ad-167">Copia el archivo de dependencia ( *\*.deps* ) de la aplicación que se está probando en la carpeta *bin* del proyecto de prueba.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-167">Copies the dependency file ( *\*.deps* ) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="9c8ad-168">Establece la raíz de contenido en la raíz de proyecto de la aplicación que se está probando, lo que permite encontrar archivos estáticos y páginas o vistas cuando se ejecutan las pruebas.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9c8ad-169">Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para optimizar el arranque de la aplicación que se está probando con [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="9c8ad-170">En la siguiente prueba se usa [xUnit](https://xunit.github.io/) para comprobar que la página de índice se carga con un código de estado correcto y con el encabezado Content-Type apropiado:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="9c8ad-171">Para más información, vea el tema [Pruebas de integración](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="9c8ad-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="9c8ad-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="9c8ad-173">ASP.NET Core 2.1 presenta nuevas convenciones de programación que hacen que sea más fácil crear y limpiar API web descriptivas.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="9c8ad-174">`ActionResult<T>` es un nuevo tipo que se ha agregado para que una aplicación pueda devolver un tipo de respuesta o cualquier otro resultado de acción (similar a IActionResult), sin dejar de indicar el tipo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="9c8ad-175">El atributo `[ApiController]` se ha agregado también como una forma de decidir si usar convenciones y comportamientos específicos de las API web.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="9c8ad-176">Para más información, vea [Compilación de API web con ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="9c8ad-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="9c8ad-177">IHttpClientFactory</span></span>

<span data-ttu-id="9c8ad-178">ASP.NET Core 2.1 incluye un nuevo servicio `IHttpClientFactory` que facilita la configuración y uso de instancias de `HttpClient` en las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="9c8ad-179">`HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="9c8ad-180">Este servicio:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-180">The factory:</span></span>

* <span data-ttu-id="9c8ad-181">Hace que el registro de instancias de `HttpClient` por cliente con nombre sea más intuitivo.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="9c8ad-182">Implementa un controlador de Polly que permite usar directivas de Polly en directivas de reintentos, de interruptores de circuitos, etc.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="9c8ad-183">Para más información, vea [Inicio de solicitudes HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="9c8ad-184">Configuración de transporte de Kestrel</span><span class="sxs-lookup"><span data-stu-id="9c8ad-184">Kestrel transport configuration</span></span>

<span data-ttu-id="9c8ad-185">Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="9c8ad-186">Para más información, consulte [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration) (Implementación del servidor web de Kestrel: configuración de transporte).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="9c8ad-187">Generador de host genérico</span><span class="sxs-lookup"><span data-stu-id="9c8ad-187">Generic host builder</span></span>

<span data-ttu-id="9c8ad-188">Se ha incluido el generador de host genérico (`HostBuilder`),</span><span class="sxs-lookup"><span data-stu-id="9c8ad-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="9c8ad-189">que se puede usar con aplicaciones que no procesan solicitudes HTTP (mensajería, tareas en segundo plano, etc.).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="9c8ad-190">Para más información, vea [Host genérico de .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="9c8ad-191">Plantillas de SPA actualizadas</span><span class="sxs-lookup"><span data-stu-id="9c8ad-191">Updated SPA templates</span></span>

<span data-ttu-id="9c8ad-192">Las plantillas de aplicación de página única para Angular, React y React con Redux se han actualizado y ahora usan sistemas de generación y estructuras de proyecto estándar en cada marco.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="9c8ad-193">La plantilla Angular se basa en la CLI de Angular, mientras que las plantillas de React se basan en create-react-app.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="9c8ad-194">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a><span data-ttu-id="9c8ad-195">Razor Pages busca recursos de Razor</span><span class="sxs-lookup"><span data-stu-id="9c8ad-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="9c8ad-196">En la versión 2.1, Razor Pages busca recursos de Razor (como diseños y líneas de código parcialmente ejecutadas) en los siguientes directorios y en el orden indicado:</span><span class="sxs-lookup"><span data-stu-id="9c8ad-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="9c8ad-197">Carpeta Current Pages</span><span class="sxs-lookup"><span data-stu-id="9c8ad-197">Current Pages folder.</span></span>
1. <span data-ttu-id="9c8ad-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="9c8ad-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="9c8ad-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="9c8ad-199">*/Views/Shared/*</span></span>

## <a name="no-locrazor-pages-in-an-area"></a><span data-ttu-id="9c8ad-200">Razor Pages en un área</span><span class="sxs-lookup"><span data-stu-id="9c8ad-200">Razor Pages in an area</span></span>

<span data-ttu-id="9c8ad-201">Razor Pages ya admite las [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="9c8ad-202">Para ver un ejemplo de áreas, cree una nueva aplicación web de Razor Pages con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="9c8ad-203">Las aplicaciones web de Razor Pages con cuentas de usuario individuales incluyen */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="9c8ad-204">Versión de compatibilidad de MVC</span><span class="sxs-lookup"><span data-stu-id="9c8ad-204">MVC compatibility version</span></span>

<span data-ttu-id="9c8ad-205">El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o una versión posterior.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="9c8ad-206">Para obtener más información, vea <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="9c8ad-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="9c8ad-207">Migración de 2.0 a 2.1</span><span class="sxs-lookup"><span data-stu-id="9c8ad-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="9c8ad-208">Vea [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21) (Migración de ASP.NET Core 2.0 a 2.1).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="9c8ad-209">Información adicional</span><span class="sxs-lookup"><span data-stu-id="9c8ad-209">Additional information</span></span>

<span data-ttu-id="9c8ad-210">Para ver la lista completa de cambios, vea las [notas de la versión de ASP.NET Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="9c8ad-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
