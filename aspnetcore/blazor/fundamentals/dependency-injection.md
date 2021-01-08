---
title: Inserción de dependencias de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo las aplicaciones Blazor pueden insertar servicios en componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3f2b4eff5422acbec80b2fd9b801101271cc3f75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97808730"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="45797-103">Inserción de dependencias de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45797-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="45797-104">Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="45797-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="45797-105">[Inserción de dependencias (DI)](xref:fundamentals/dependency-injection) es una técnica para acceder a los servicios configurados en una ubicación central:</span><span class="sxs-lookup"><span data-stu-id="45797-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="45797-106">Los servicios registrados en el marco se pueden insertar directamente en los componentes de aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="45797-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="45797-107">Las aplicaciones de Blazor definen y registran servicios personalizados y los ponen a disposición de toda la aplicación a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="45797-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="45797-108">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="45797-108">Default services</span></span>

<span data-ttu-id="45797-109">Los servicios que se muestran en la tabla siguiente se usan normalmente en aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="45797-109">The services shown the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="45797-110">web de Office</span><span class="sxs-lookup"><span data-stu-id="45797-110">Service</span></span> | <span data-ttu-id="45797-111">Período de duración</span><span class="sxs-lookup"><span data-stu-id="45797-111">Lifetime</span></span> | <span data-ttu-id="45797-112">Descripción</span><span class="sxs-lookup"><span data-stu-id="45797-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="45797-113">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="45797-113">Scoped</span></span> | <p><span data-ttu-id="45797-114">Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</span><span class="sxs-lookup"><span data-stu-id="45797-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="45797-115">La instancia de <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="45797-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="45797-116">Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="45797-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="45797-117">Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="45797-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="45797-118">Para obtener más información, vea <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="45797-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="45797-119">Un <xref:System.Net.Http.HttpClient> se registra como un servicio con ámbito, no como singleton.</span><span class="sxs-lookup"><span data-stu-id="45797-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="45797-120">Para más información, consulte la sección [Duración del servicio](#service-lifetime).</span><span class="sxs-lookup"><span data-stu-id="45797-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="45797-121">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="45797-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="45797-122">**Blazor Server** : Con ámbito</span><span class="sxs-lookup"><span data-stu-id="45797-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="45797-123">Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="45797-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="45797-124">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="45797-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="45797-125">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="45797-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="45797-126">**Blazor Server** : Con ámbito</span><span class="sxs-lookup"><span data-stu-id="45797-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="45797-127">Contiene asistentes para trabajar con URI y el estado de navegación.</span><span class="sxs-lookup"><span data-stu-id="45797-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="45797-128">Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="45797-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="45797-129">Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla.</span><span class="sxs-lookup"><span data-stu-id="45797-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="45797-130">Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="45797-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="45797-131">Adición de servicios a una aplicación</span><span class="sxs-lookup"><span data-stu-id="45797-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="45797-132">Configure los servicios de la colección de servicios de la aplicación en el método `Program.Main` de `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="45797-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="45797-133">En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="45797-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="45797-134">Después de compilar el host, los servicios están disponibles en el ámbito de inserción de dependencias raíz antes de que se representen los componentes.</span><span class="sxs-lookup"><span data-stu-id="45797-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="45797-135">Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="45797-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="45797-136">El host también proporciona una instancia de configuración central para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45797-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="45797-137">En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, `appsettings.json`) a `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="45797-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="45797-138">Después de crear una aplicación, examine el método `Startup.ConfigureServices` en `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="45797-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="45797-139">Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de [descriptor de servicio](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor).</span><span class="sxs-lookup"><span data-stu-id="45797-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="45797-140">Para agregar los servicios al método `ConfigureServices`, se proporcionan descriptores de servicio a la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="45797-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="45797-141">En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="45797-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="45797-142">Duración del servicio</span><span class="sxs-lookup"><span data-stu-id="45797-142">Service lifetime</span></span>

<span data-ttu-id="45797-143">Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="45797-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="45797-144">Período de duración</span><span class="sxs-lookup"><span data-stu-id="45797-144">Lifetime</span></span> | <span data-ttu-id="45797-145">Descripción</span><span class="sxs-lookup"><span data-stu-id="45797-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="45797-146">Las aplicaciones Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="45797-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="45797-147">Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="45797-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="45797-148">El modelo de hospedaje de Blazor Server admite la duración `Scoped` a través de las solicitudes HTTP, pero no a través de los mensajes de circuito/conexión de SignalR entre los componentes cargados en el cliente.</span><span class="sxs-lookup"><span data-stu-id="45797-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="45797-149">Por lo general, Razor Razor o la parte de MVC de la aplicación trata los servicios con ámbito y vuelve a crear los servicios en *cada solicitud HTTP* al navegar entre páginas o vistas, o desde una página o vista a un componente.</span><span class="sxs-lookup"><span data-stu-id="45797-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="45797-150">Los servicios con ámbito no se reconstruyen al navegar entre los componentes del cliente, donde la comunicación con el servidor se realiza a través de la conexión SignalR del circuito del usuario, no a través de solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="45797-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="45797-151">En los escenarios de componentes del cliente siguientes, los servicios con ámbito se reconstruyen porque se crea un circuito nuevo para el usuario:</span><span class="sxs-lookup"><span data-stu-id="45797-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="45797-152">El usuario cierra la ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="45797-152">The user closes the browser's window.</span></span> <span data-ttu-id="45797-153">El usuario abre una ventana nueva y vuelve a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45797-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="45797-154">El usuario cierra la última pestaña de la aplicación en una ventana del explorador.</span><span class="sxs-lookup"><span data-stu-id="45797-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="45797-155">El usuario abre una pestaña nueva y vuelve a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="45797-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="45797-156">El usuario selecciona el botón de recargar o actualizar del explorador.</span><span class="sxs-lookup"><span data-stu-id="45797-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="45797-157">Para más información sobre cómo conservar el estado del usuario en los distintos servicios con ámbito en las aplicaciones Blazor Server, consulte <xref:blazor/hosting-models?pivots=server>.</span><span class="sxs-lookup"><span data-stu-id="45797-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="45797-158">La inserción de dependencias crea una *sola instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="45797-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="45797-159">Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio.</span><span class="sxs-lookup"><span data-stu-id="45797-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="45797-160">Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="45797-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="45797-161">El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="45797-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="45797-162">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="45797-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="45797-163">Solicitud de un servicio en un componente</span><span class="sxs-lookup"><span data-stu-id="45797-163">Request a service in a component</span></span>

<span data-ttu-id="45797-164">Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [`@inject`](xref:mvc/views/razor#inject) de Razor, que tiene dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="45797-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="45797-165">Tipo: el tipo de servicio que se va a insertar.</span><span class="sxs-lookup"><span data-stu-id="45797-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="45797-166">Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado.</span><span class="sxs-lookup"><span data-stu-id="45797-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="45797-167">La propiedad no requiere la creación manual.</span><span class="sxs-lookup"><span data-stu-id="45797-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="45797-168">El compilador crea la propiedad.</span><span class="sxs-lookup"><span data-stu-id="45797-168">The compiler creates the property.</span></span>

<span data-ttu-id="45797-169">Para obtener más información, vea <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="45797-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="45797-170">Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.</span><span class="sxs-lookup"><span data-stu-id="45797-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="45797-171">En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="45797-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="45797-172">El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="45797-173">Observe cómo el código solo usa la abstracción de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="45797-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="45797-174">De forma interna, la propiedad generada (`DataRepository`) usa el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="45797-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="45797-175">Normalmente, este atributo no se usa de manera directa.</span><span class="sxs-lookup"><span data-stu-id="45797-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="45797-176">Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span><span class="sxs-lookup"><span data-stu-id="45797-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="45797-177">En los componentes derivados de la clase base, la directiva [`@inject`](xref:mvc/views/razor#inject) no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="45797-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="45797-178">Es suficiente con el objeto <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la clase base:</span><span class="sxs-lookup"><span data-stu-id="45797-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="45797-179">Uso de la inserción de dependencias en servicios</span><span class="sxs-lookup"><span data-stu-id="45797-179">Use DI in services</span></span>

<span data-ttu-id="45797-180">Es posible que los servicios complejos requieran servicios adicionales.</span><span class="sxs-lookup"><span data-stu-id="45797-180">Complex services might require additional services.</span></span> <span data-ttu-id="45797-181">En el ejemplo siguiente, `DataAccess` requiere el servicio predeterminado <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="45797-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="45797-182">[`@inject`](xref:mvc/views/razor#inject) (o el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios.</span><span class="sxs-lookup"><span data-stu-id="45797-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="45797-183">En su lugar se debe usar la *inserción de constructores*.</span><span class="sxs-lookup"><span data-stu-id="45797-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="45797-184">Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="45797-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="45797-185">Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="45797-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="45797-186">En el ejemplo siguiente, el constructor recibe <xref:System.Net.Http.HttpClient> a través de DI.</span><span class="sxs-lookup"><span data-stu-id="45797-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="45797-187"><xref:System.Net.Http.HttpClient> es un servicio predeterminado.</span><span class="sxs-lookup"><span data-stu-id="45797-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="45797-188">Requisitos previos para la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="45797-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="45797-189">Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="45797-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="45797-190">Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="45797-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="45797-191">El constructor aplicable debe ser `public`.</span><span class="sxs-lookup"><span data-stu-id="45797-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="45797-192">Debe existir un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="45797-192">One applicable constructor must exist.</span></span> <span data-ttu-id="45797-193">En caso de ambigüedad, la inserción de dependencias inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="45797-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="45797-194">Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="45797-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="45797-195">En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="45797-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="45797-196">Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios.</span><span class="sxs-lookup"><span data-stu-id="45797-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="45797-197">En las aplicaciones Blazor Server, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="45797-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="45797-198">En las aplicaciones Blazor WebAssembly, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.</span><span class="sxs-lookup"><span data-stu-id="45797-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="45797-199">Para detectar servicios transitorios descartables en una aplicación, consulte la sección [Detección de transitorios descartables](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="45797-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="45797-200">Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="45797-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="45797-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un tipo abstracto derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="45797-202">Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente.</span><span class="sxs-lookup"><span data-stu-id="45797-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="45797-203">Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="45797-204">Esto puede ser útil para servicios que:</span><span class="sxs-lookup"><span data-stu-id="45797-204">This can be useful for services that:</span></span>

* <span data-ttu-id="45797-205">Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="45797-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="45797-206">No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="45797-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="45797-207">Hay dos versiones del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase> disponibles:</span><span class="sxs-lookup"><span data-stu-id="45797-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="45797-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un elemento secundario abstracto y descartable del tipo <xref:Microsoft.AspNetCore.Components.ComponentBase> con una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> protegida de tipo <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="45797-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="45797-209">Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="45797-210">Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el atributo[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="45797-211">Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>.</span><span class="sxs-lookup"><span data-stu-id="45797-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="45797-212">Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.</span><span class="sxs-lookup"><span data-stu-id="45797-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="45797-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y agrega una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito.</span><span class="sxs-lookup"><span data-stu-id="45797-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="45797-214">Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de <xref:System.IServiceProvider> cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="45797-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="45797-215">La propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="45797-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="45797-216">Uso de un objeto DbContext de Entity Framework Core (EF Core) desde la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="45797-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="45797-217">Para obtener más información, vea <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="45797-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="45797-218">Detección de transitorios descartables</span><span class="sxs-lookup"><span data-stu-id="45797-218">Detect transient disposables</span></span>

<span data-ttu-id="45797-219">En los siguientes ejemplos se muestra cómo detectar servicios transitorios descartables en una aplicación que debe usar <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="45797-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="45797-220">Para obtener más información, consulte la sección [Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="45797-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="45797-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="45797-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="45797-222">En el ejemplo siguiente se detecta `TransientDisposable` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="45797-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="45797-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="45797-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="45797-224">Agregue el espacio de nombres de <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> a `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45797-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="45797-225">En `Program.CreateHostBuilder` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45797-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="45797-226">En el ejemplo siguiente se detecta `TransientDependency` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="45797-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

<span data-ttu-id="45797-227">La aplicación puede registrar transitorios descartables sin producir una excepción.</span><span class="sxs-lookup"><span data-stu-id="45797-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="45797-228">Sin embargo, intentar resolver un transitorio descartable resulta en una clase <xref:System.InvalidOperationException>, como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="45797-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="45797-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="45797-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="45797-230">Navegue al componente `TransientDisposable` en `/transient-disposable`, y se lanza una clase <xref:System.InvalidOperationException> cuando el marco intenta construir una instancia de `TransientDisposable`:</span><span class="sxs-lookup"><span data-stu-id="45797-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="45797-231">System.InvalidOperationException: intentando resolver el servicio del transitorio descartable TransientDisposable en el ámbito incorrecto.</span><span class="sxs-lookup"><span data-stu-id="45797-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="45797-232">Use una clase base de componente "OwningComponentBase\<T>" para el servicio "T" que está intentando resolver.</span><span class="sxs-lookup"><span data-stu-id="45797-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="45797-233">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="45797-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="45797-234">Instrucciones de `IDisposable` para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="45797-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
