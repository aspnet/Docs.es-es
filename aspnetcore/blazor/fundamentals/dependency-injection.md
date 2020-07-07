---
title: Inserción de dependencias de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo las aplicaciones Blazor pueden insertar servicios en componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 0e99e2e3e2dafae0c35d2cfe6903bf4f511f5dc1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402889"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="c8e5f-103">Inserción de dependencias de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8e5f-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="c8e5f-104">Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="c8e5f-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="c8e5f-105"> admite la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c8e5f-106">Las aplicaciones pueden usar servicios integrados mediante su inserción en componentes.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="c8e5f-107">Las aplicaciones también pueden definir y registrar servicios personalizados y hacer que estén disponibles en toda la aplicación a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="c8e5f-108">La inserción de dependencias es una técnica para acceder a los servicios configurados en una ubicación central.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="c8e5f-109">Esto puede ser útil en aplicaciones Blazor para:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="c8e5f-110">Compartir una única instancia de una clase de servicio entre varios componentes, lo que se conoce como servicio *singleton*.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="c8e5f-111">Desacoplar componentes de clases de servicio concretas mediante abstracciones de referencia.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="c8e5f-112">Por ejemplo, considere una interfaz `IDataAccess` para acceder a los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="c8e5f-113">La interfaz se implementa mediante una clase `DataAccess` concreta y se registra como un servicio en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="c8e5f-114">Cuando un componente usa la inserción de dependencias para recibir una implementación de `IDataAccess`, el componente no se acopla al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="c8e5f-115">La implementación se puede intercambiar, posiblemente para una implementación ficticia en pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="c8e5f-116">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="c8e5f-116">Default services</span></span>

<span data-ttu-id="c8e5f-117">Los servicios predeterminados se agregan de forma automática a la colección de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="c8e5f-118">web de Office</span><span class="sxs-lookup"><span data-stu-id="c8e5f-118">Service</span></span> | <span data-ttu-id="c8e5f-119">Período de duración</span><span class="sxs-lookup"><span data-stu-id="c8e5f-119">Lifetime</span></span> | <span data-ttu-id="c8e5f-120">Descripción</span><span class="sxs-lookup"><span data-stu-id="c8e5f-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="c8e5f-121">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c8e5f-121">Transient</span></span> | <span data-ttu-id="c8e5f-122">Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="c8e5f-123">La instancia de <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="c8e5f-124">Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="c8e5f-125">Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="c8e5f-126">Para obtener más información, vea <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="c8e5f-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="c8e5f-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="c8e5f-128">Con ámbito (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="c8e5f-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="c8e5f-129">Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="c8e5f-130">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="c8e5f-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="c8e5f-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="c8e5f-132">Con ámbito (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="c8e5f-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="c8e5f-133">Contiene asistentes para trabajar con URI y el estado de navegación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="c8e5f-134">Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="c8e5f-135">Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="c8e5f-136">Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="c8e5f-137">Adición de servicios a una aplicación</span><span class="sxs-lookup"><span data-stu-id="c8e5f-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="c8e5f-138">Configure los servicios de la colección de servicios de la aplicación en el método `Main` de `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="c8e5f-139">En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="c8e5f-140">Una vez que se ha compilado el host, se puede acceder a los servicios desde el ámbito de inserción de dependencias raíz antes de que se representen los componentes.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="c8e5f-141">Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-141">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="c8e5f-142">El host también proporciona una instancia de configuración central para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="c8e5f-143">En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, `appsettings.json`) a `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="c8e5f-144">Después de crear una aplicación, examine el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="c8e5f-145">Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de descriptor de servicio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="c8e5f-146">Para agregar los servicios, se proporcionan descriptores de servicio a la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-146">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="c8e5f-147">En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="c8e5f-148">Duración del servicio</span><span class="sxs-lookup"><span data-stu-id="c8e5f-148">Service lifetime</span></span>

<span data-ttu-id="c8e5f-149">Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="c8e5f-150">Período de duración</span><span class="sxs-lookup"><span data-stu-id="c8e5f-150">Lifetime</span></span> | <span data-ttu-id="c8e5f-151">Descripción</span><span class="sxs-lookup"><span data-stu-id="c8e5f-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="c8e5f-152">Las aplicaciones Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-152">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="c8e5f-153">Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="c8e5f-154">Pero el modelo de hospedaje de Blazor Server admite la duración `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="c8e5f-155">En las aplicaciones Blazor Server, el ámbito del registro de un servicio con ámbito es la *conexión*.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="c8e5f-156">Por este motivo, se prefiere el uso de servicios con ámbito para los servicios que deben tener el ámbito del usuario actual, aunque la intención actual sea ejecutar el lado cliente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="c8e5f-157">La inserción de dependencias crea una *sola instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="c8e5f-158">Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="c8e5f-159">Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="c8e5f-160">El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="c8e5f-161">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="c8e5f-162">Solicitud de un servicio en un componente</span><span class="sxs-lookup"><span data-stu-id="c8e5f-162">Request a service in a component</span></span>

<span data-ttu-id="c8e5f-163">Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [\@inject](xref:mvc/views/razor#inject) de Razor.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="c8e5f-164">[`@inject`](xref:mvc/views/razor#inject) tiene dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="c8e5f-165">Tipo: el tipo de servicio que se va a insertar.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="c8e5f-166">Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="c8e5f-167">La propiedad no requiere la creación manual.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="c8e5f-168">El compilador crea la propiedad.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-168">The compiler creates the property.</span></span>

<span data-ttu-id="c8e5f-169">Para obtener más información, vea <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="c8e5f-170">Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="c8e5f-171">En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="c8e5f-172">El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="c8e5f-173">Observe cómo el código solo usa la abstracción de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="c8e5f-174">De forma interna, la propiedad generada (`DataRepository`) usa el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="c8e5f-175">Normalmente, este atributo no se usa de manera directa.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="c8e5f-176">Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span><span class="sxs-lookup"><span data-stu-id="c8e5f-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="c8e5f-177">En los componentes derivados de la clase base, la directiva [`@inject`](xref:mvc/views/razor#inject) no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="c8e5f-178">Es suficiente con el objeto <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la clase base:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="c8e5f-179">Uso de la inserción de dependencias en servicios</span><span class="sxs-lookup"><span data-stu-id="c8e5f-179">Use DI in services</span></span>

<span data-ttu-id="c8e5f-180">Es posible que los servicios complejos requieran servicios adicionales.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-180">Complex services might require additional services.</span></span> <span data-ttu-id="c8e5f-181">En el ejemplo anterior, `DataAccess` podría requerir el servicio predeterminado <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="c8e5f-182">[`@inject`](xref:mvc/views/razor#inject) (o el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="c8e5f-183">En su lugar se debe usar la *inserción de constructores*.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="c8e5f-184">Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="c8e5f-185">Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="c8e5f-186">Requisitos previos para la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-186">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="c8e5f-187">Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-187">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="c8e5f-188">Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-188">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="c8e5f-189">El constructor aplicable debe ser `public`.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-189">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="c8e5f-190">Debe existir un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-190">One applicable constructor must exist.</span></span> <span data-ttu-id="c8e5f-191">En caso de ambigüedad, la inserción de dependencias inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-191">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="c8e5f-192">Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c8e5f-192">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="c8e5f-193">En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-193">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="c8e5f-194">Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-194">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="c8e5f-195">En las aplicaciones Blazor Server, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-195">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="c8e5f-196">En las aplicaciones Blazor WebAssembly, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-196">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="c8e5f-197">Para detectar servicios transitorios descartables en una aplicación, consulte la sección [Detección de transitorios descartables](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-197">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="c8e5f-198">Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-198">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="c8e5f-199"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un tipo abstracto derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-199"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="c8e5f-200">Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-200">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="c8e5f-201">Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-201">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="c8e5f-202">Esto puede ser útil para servicios que:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-202">This can be useful for services that:</span></span>

* <span data-ttu-id="c8e5f-203">Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-203">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="c8e5f-204">No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-204">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="c8e5f-205">Hay dos versiones del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase> disponibles:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-205">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="c8e5f-206"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un elemento secundario abstracto y descartable del tipo <xref:Microsoft.AspNetCore.Components.ComponentBase> con una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> protegida de tipo <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-206"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c8e5f-207">Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-207">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="c8e5f-208">Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el atributo[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-208">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="c8e5f-209">Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-209">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="c8e5f-210">Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-210">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="c8e5f-211"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y agrega una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-211"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="c8e5f-212">Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de <xref:System.IServiceProvider> cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-212">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="c8e5f-213">La propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-213">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="c8e5f-214">Uso de DbContext de Entity Framework desde la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c8e5f-214">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="c8e5f-215">Un tipo de servicio común que se puede recuperar desde la inserción de dependencias en aplicaciones web es el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-215">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="c8e5f-216">El registro de servicios de EF mediante <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> agrega el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> como un servicio con ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-216">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="c8e5f-217">El registro como un servicio con ámbito puede dar lugar a problemas en aplicaciones Blazor porque hace que las instancias de <xref:Microsoft.EntityFrameworkCore.DbContext> sean de larga duración y se compartan a través de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-217">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="c8e5f-218"><xref:Microsoft.EntityFrameworkCore.DbContext> no es seguro para subprocesos y no se debe usar de manera simultánea.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-218"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="c8e5f-219">En función de la aplicación, el uso de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> para limitar el ámbito de un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> a un componente único *puede* resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-219">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="c8e5f-220">Si un componente no usa un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> en paralelo, es suficiente con la derivación del componente de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y la recuperación del objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> porque garantiza lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-220">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="c8e5f-221">Los componentes independientes no comparten un objeto <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-221">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="c8e5f-222">El objeto <xref:Microsoft.EntityFrameworkCore.DbContext> dura tanto como el componente que depende de él.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-222">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="c8e5f-223">Si es posible que un único componente use un objeto <xref:Microsoft.EntityFrameworkCore.DbContext> de forma simultánea (por ejemplo, cada vez que un usuario selecciona un botón), incluso el uso de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> no evita problemas con las operaciones de EF simultáneas.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-223">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="c8e5f-224">En ese caso, use otro objeto <xref:Microsoft.EntityFrameworkCore.DbContext> para cada operación de EF lógica.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-224">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="c8e5f-225">Use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-225">Use either of the following approaches:</span></span>

* <span data-ttu-id="c8e5f-226">Cree el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> directamente con <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> como argumento, que se puede recuperar mediante la inserción de dependencias y es seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-226">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="c8e5f-227">Registre el objeto <xref:Microsoft.EntityFrameworkCore.DbContext> en el contenedor de servicios con una duración transitoria:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-227">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="c8e5f-228">Al registrar el contexto, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-228">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c8e5f-229">El método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> toma dos parámetros opcionales de tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-229">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="c8e5f-230">Para usar este enfoque, solo el parámetro `contextLifetime` tiene que ser <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-230">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c8e5f-231">`optionsLifetime` puede conservar su valor predeterminado de <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-231">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="c8e5f-232">El objeto <xref:Microsoft.EntityFrameworkCore.DbContext> transitorio se puede insertar de la forma habitual (mediante [`@inject`](xref:mvc/views/razor#inject)) en componentes que no ejecutarán varias operaciones de EF en paralelo.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-232">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="c8e5f-233">Los que puedan realizar varias operaciones de EF simultáneamente pueden solicitar objetos <xref:Microsoft.EntityFrameworkCore.DbContext> independientes para cada operación paralela mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-233">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="detect-transient-disposables"></a><span data-ttu-id="c8e5f-234">Detección de transitorios descartables</span><span class="sxs-lookup"><span data-stu-id="c8e5f-234">Detect transient disposables</span></span>

<span data-ttu-id="c8e5f-235">En los siguientes ejemplos se muestra cómo detectar servicios transitorios descartables en una aplicación que debe usar <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="c8e5f-235">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="c8e5f-236">Para obtener más información, consulte la sección [Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="c8e5f-236">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="c8e5f-237">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-237">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="c8e5f-238">En el ejemplo siguiente se detecta `TransientDisposable` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="c8e5f-238">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="c8e5f-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="c8e5f-240">`Program`:</span><span class="sxs-lookup"><span data-stu-id="c8e5f-240">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="c8e5f-241">En el ejemplo siguiente se detecta `TransientDependency` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="c8e5f-241">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="c8e5f-242">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c8e5f-242">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="c8e5f-243">Instrucciones de `IDisposable` para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="c8e5f-243">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
