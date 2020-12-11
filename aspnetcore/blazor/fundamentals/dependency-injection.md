---
title: Inserción de dependencias de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo las aplicaciones Blazor pueden insertar servicios en componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556520"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="9d2d1-103">Inserción de dependencias de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d2d1-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="9d2d1-104">Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="9d2d1-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="9d2d1-105">Blazor admite la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d2d1-106">Las aplicaciones pueden usar servicios integrados mediante su inserción en componentes.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="9d2d1-107">Las aplicaciones también pueden definir y registrar servicios personalizados y hacer que estén disponibles en toda la aplicación a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="9d2d1-108">La inserción de dependencias es una técnica para acceder a los servicios configurados en una ubicación central.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="9d2d1-109">Esto puede ser útil en aplicaciones Blazor para:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="9d2d1-110">Compartir una única instancia de una clase de servicio entre varios componentes, lo que se conoce como servicio *singleton*.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="9d2d1-111">Desacoplar componentes de clases de servicio concretas mediante abstracciones de referencia.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="9d2d1-112">Por ejemplo, considere una interfaz `IDataAccess` para acceder a los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="9d2d1-113">La interfaz se implementa mediante una clase `DataAccess` concreta y se registra como un servicio en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="9d2d1-114">Cuando un componente usa la inserción de dependencias para recibir una implementación de `IDataAccess`, el componente no se acopla al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="9d2d1-115">La implementación se puede intercambiar, posiblemente para una implementación ficticia en pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="9d2d1-116">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="9d2d1-116">Default services</span></span>

<span data-ttu-id="9d2d1-117">Los servicios predeterminados se agregan de forma automática a la colección de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="9d2d1-118">web de Office</span><span class="sxs-lookup"><span data-stu-id="9d2d1-118">Service</span></span> | <span data-ttu-id="9d2d1-119">Período de duración</span><span class="sxs-lookup"><span data-stu-id="9d2d1-119">Lifetime</span></span> | <span data-ttu-id="9d2d1-120">Descripción</span><span class="sxs-lookup"><span data-stu-id="9d2d1-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="9d2d1-121">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="9d2d1-121">Scoped</span></span> | <span data-ttu-id="9d2d1-122">Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="9d2d1-123">La instancia de <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="9d2d1-124">Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="9d2d1-125">Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="9d2d1-126">Para obtener más información, vea <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="9d2d1-127">Un <xref:System.Net.Http.HttpClient> se registra como un servicio con ámbito, no como singleton.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="9d2d1-128">Para más información, consulte la sección [Duración del servicio](#service-lifetime).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="9d2d1-129">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="9d2d1-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="9d2d1-130">Con ámbito (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9d2d1-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="9d2d1-131">Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="9d2d1-132">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="9d2d1-133">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="9d2d1-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="9d2d1-134">Con ámbito (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9d2d1-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="9d2d1-135">Contiene asistentes para trabajar con URI y el estado de navegación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="9d2d1-136">Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="9d2d1-137">Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="9d2d1-138">Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="9d2d1-139">Adición de servicios a una aplicación</span><span class="sxs-lookup"><span data-stu-id="9d2d1-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="9d2d1-140">Configure los servicios de la colección de servicios de la aplicación en el método `Main` de `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="9d2d1-141">En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="9d2d1-142">Una vez que se ha compilado el host, se puede acceder a los servicios desde el ámbito de inserción de dependencias raíz antes de que se representen los componentes.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="9d2d1-143">Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="9d2d1-144">El host también proporciona una instancia de configuración central para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="9d2d1-145">En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, `appsettings.json`) a `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="9d2d1-146">Después de crear una aplicación, examine el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="9d2d1-147">Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de descriptor de servicio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="9d2d1-148">Para agregar los servicios al método `ConfigureServices`, se proporcionan descriptores de servicio a la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="9d2d1-149">En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="9d2d1-150">Duración del servicio</span><span class="sxs-lookup"><span data-stu-id="9d2d1-150">Service lifetime</span></span>

<span data-ttu-id="9d2d1-151">Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="9d2d1-152">Período de duración</span><span class="sxs-lookup"><span data-stu-id="9d2d1-152">Lifetime</span></span> | <span data-ttu-id="9d2d1-153">Descripción</span><span class="sxs-lookup"><span data-stu-id="9d2d1-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="9d2d1-154">Las aplicaciones Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="9d2d1-155">Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="9d2d1-156">El modelo de hospedaje de Blazor Server admite la duración `Scoped` a través de las solicitudes HTTP, pero no a través de los mensajes de circuito/conexión de SingalR entre los componentes cargados en el cliente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="9d2d1-157">Por lo general, Razor Razor o la parte de MVC de la aplicación trata los servicios con ámbito y vuelve a crear los servicios en *cada solicitud HTTP* al navegar entre páginas o vistas, o desde una página o vista a un componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="9d2d1-158">Los servicios con ámbito no se reconstruyen al navegar entre los componentes del cliente, donde la comunicación con el servidor se realiza a través de la conexión SignalR del circuito del usuario, no a través de solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="9d2d1-159">En los escenarios de componentes del cliente siguientes, los servicios con ámbito se reconstruyen porque se crea un circuito nuevo para el usuario:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="9d2d1-160">El usuario cierra la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-160">The user closes the browser's window.</span></span> <span data-ttu-id="9d2d1-161">El usuario abre una ventana nueva y vuelve a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="9d2d1-162">El usuario cierra la última pestaña de la aplicación en una ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="9d2d1-163">El usuario abre una pestaña nueva y vuelve a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="9d2d1-164">El usuario selecciona el botón de recargar o actualizar del explorador.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="9d2d1-165">Para más información sobre cómo conservar el estado del usuario en los distintos servicios con ámbito en las aplicaciones Blazor Server, consulte <xref:blazor/hosting-models?pivots=server>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="9d2d1-166">La inserción de dependencias crea una *sola instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="9d2d1-167">Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="9d2d1-168">Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="9d2d1-169">El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="9d2d1-170">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="9d2d1-171">Solicitud de un servicio en un componente</span><span class="sxs-lookup"><span data-stu-id="9d2d1-171">Request a service in a component</span></span>

<span data-ttu-id="9d2d1-172">Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [\@inject](xref:mvc/views/razor#inject) de Razor.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="9d2d1-173">[`@inject`](xref:mvc/views/razor#inject) tiene dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="9d2d1-174">Tipo: el tipo de servicio que se va a insertar.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="9d2d1-175">Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="9d2d1-176">La propiedad no requiere la creación manual.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="9d2d1-177">El compilador crea la propiedad.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-177">The compiler creates the property.</span></span>

<span data-ttu-id="9d2d1-178">Para obtener más información, vea <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="9d2d1-179">Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="9d2d1-180">En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="9d2d1-181">El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="9d2d1-182">Observe cómo el código solo usa la abstracción de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="9d2d1-183">De forma interna, la propiedad generada (`DataRepository`) usa el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="9d2d1-184">Normalmente, este atributo no se usa de manera directa.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="9d2d1-185">Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span><span class="sxs-lookup"><span data-stu-id="9d2d1-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="9d2d1-186">En los componentes derivados de la clase base, la directiva [`@inject`](xref:mvc/views/razor#inject) no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="9d2d1-187">Es suficiente con el objeto <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la clase base:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="9d2d1-188">Uso de la inserción de dependencias en servicios</span><span class="sxs-lookup"><span data-stu-id="9d2d1-188">Use DI in services</span></span>

<span data-ttu-id="9d2d1-189">Es posible que los servicios complejos requieran servicios adicionales.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-189">Complex services might require additional services.</span></span> <span data-ttu-id="9d2d1-190">En el ejemplo anterior, `DataAccess` podría requerir el servicio predeterminado <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="9d2d1-191">[`@inject`](xref:mvc/views/razor#inject) (o el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="9d2d1-192">En su lugar se debe usar la *inserción de constructores*.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="9d2d1-193">Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="9d2d1-194">Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="9d2d1-195">En el ejemplo siguiente, el constructor recibe <xref:System.Net.Http.HttpClient> a través de DI.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="9d2d1-196"><xref:System.Net.Http.HttpClient> es un servicio predeterminado.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="9d2d1-197">Requisitos previos para la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="9d2d1-198">Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="9d2d1-199">Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="9d2d1-200">El constructor aplicable debe ser `public`.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="9d2d1-201">Debe existir un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-201">One applicable constructor must exist.</span></span> <span data-ttu-id="9d2d1-202">En caso de ambigüedad, la inserción de dependencias inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="9d2d1-203">Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="9d2d1-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="9d2d1-204">En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="9d2d1-205">Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="9d2d1-206">En las aplicaciones Blazor Server, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="9d2d1-207">En las aplicaciones Blazor WebAssembly, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="9d2d1-208">Para detectar servicios transitorios descartables en una aplicación, consulte la sección [Detección de transitorios descartables](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="9d2d1-209">Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="9d2d1-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un tipo abstracto derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="9d2d1-211">Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="9d2d1-212">Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="9d2d1-213">Esto puede ser útil para servicios que:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-213">This can be useful for services that:</span></span>

* <span data-ttu-id="9d2d1-214">Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="9d2d1-215">No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="9d2d1-216">Hay dos versiones del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase> disponibles:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="9d2d1-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un elemento secundario abstracto y descartable del tipo <xref:Microsoft.AspNetCore.Components.ComponentBase> con una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> protegida de tipo <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="9d2d1-218">Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="9d2d1-219">Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el atributo[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="9d2d1-220">Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="9d2d1-221">Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="9d2d1-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y agrega una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="9d2d1-223">Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de <xref:System.IServiceProvider> cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="9d2d1-224">La propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="9d2d1-225">Uso de un objeto DbContext de Entity Framework Core (EF Core) desde la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="9d2d1-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="9d2d1-226">Para obtener más información, vea <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="9d2d1-227">Detección de transitorios descartables</span><span class="sxs-lookup"><span data-stu-id="9d2d1-227">Detect transient disposables</span></span>

<span data-ttu-id="9d2d1-228">En los siguientes ejemplos se muestra cómo detectar servicios transitorios descartables en una aplicación que debe usar <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="9d2d1-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="9d2d1-229">Para obtener más información, consulte la sección [Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="9d2d1-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="9d2d1-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="9d2d1-231">En el ejemplo siguiente se detecta `TransientDisposable` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="9d2d1-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="9d2d1-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="9d2d1-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="9d2d1-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="9d2d1-234">En el ejemplo siguiente se detecta `TransientDependency` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="9d2d1-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="9d2d1-235">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9d2d1-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="9d2d1-236">Instrucciones de `IDisposable` para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="9d2d1-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
