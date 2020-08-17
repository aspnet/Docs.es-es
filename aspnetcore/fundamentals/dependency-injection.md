---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130678"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="06c06-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06c06-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06c06-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="06c06-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="06c06-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="06c06-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="06c06-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="06c06-107">Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="06c06-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="06c06-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06c06-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="06c06-109">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="06c06-109">Overview of dependency injection</span></span>

<span data-ttu-id="06c06-110">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="06c06-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="06c06-111">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="06c06-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="06c06-112">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="06c06-113">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="06c06-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="06c06-114">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="06c06-115">Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="06c06-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="06c06-116">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="06c06-117">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="06c06-118">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="06c06-119">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="06c06-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="06c06-120">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="06c06-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="06c06-121">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="06c06-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="06c06-122">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="06c06-123">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="06c06-124">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="06c06-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="06c06-125">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="06c06-126">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="06c06-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="06c06-127">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="06c06-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="06c06-128">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="06c06-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="06c06-129">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="06c06-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="06c06-130">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="06c06-131">El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="06c06-132">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="06c06-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="06c06-133">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="06c06-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="06c06-134">Con el patrón de DI:</span><span class="sxs-lookup"><span data-stu-id="06c06-134">With the DI pattern:</span></span>

* <span data-ttu-id="06c06-135">El controlador no usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="06c06-136">Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.</span><span class="sxs-lookup"><span data-stu-id="06c06-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="06c06-137">El controlador no crea una instancia de `MyDependency`, la crea el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="06c06-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="06c06-138">La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:</span><span class="sxs-lookup"><span data-stu-id="06c06-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="06c06-139">El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="06c06-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="06c06-140">`MyDependency2` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en el constructor.</span><span class="sxs-lookup"><span data-stu-id="06c06-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="06c06-141">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="06c06-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="06c06-142">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="06c06-143">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="06c06-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="06c06-144">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="06c06-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="06c06-145">`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="06c06-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="06c06-146">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="06c06-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="06c06-147">En la terminología de la inserción de dependencias, un servicio:</span><span class="sxs-lookup"><span data-stu-id="06c06-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="06c06-148">Normalmente es un objeto que proporciona un servicio a otro código de la aplicación, como el servicio `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="06c06-149">No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.</span><span class="sxs-lookup"><span data-stu-id="06c06-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="06c06-150">El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido.</span><span class="sxs-lookup"><span data-stu-id="06c06-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="06c06-151">Las implementaciones de `IMyDependency` se escribieron para mostrar la inserción de DI básica, no para implementar el registro.</span><span class="sxs-lookup"><span data-stu-id="06c06-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="06c06-152">La mayoría de las aplicaciones no deberían tener que escribir registradores.</span><span class="sxs-lookup"><span data-stu-id="06c06-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="06c06-153">En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="06c06-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="06c06-154">Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo:</span><span class="sxs-lookup"><span data-stu-id="06c06-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="06c06-155">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="06c06-155">Services injected into Startup</span></span>

<span data-ttu-id="06c06-156">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="06c06-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="06c06-157">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="06c06-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="06c06-158">Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="06c06-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="06c06-159">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="06c06-159">Register additional services with extension methods</span></span>

<span data-ttu-id="06c06-160">Cuando un método de extensión de colección de servicios está disponible para registrar un servicio:</span><span class="sxs-lookup"><span data-stu-id="06c06-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="06c06-161">La convención es usar un único método de extensión de `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por ese servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="06c06-162">Los servicios dependientes también se registran.</span><span class="sxs-lookup"><span data-stu-id="06c06-162">The dependent services are also registered.</span></span>

<span data-ttu-id="06c06-163">El código siguiente lo genera la plantilla de Razor Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="06c06-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="06c06-164">Para obtener más información, vea <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> y <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="06c06-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="06c06-165">Vea la sección [Combinación de colecciones de servicios](#csc) para obtener instrucciones sobre cómo escribir un método de extensión para registrar servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="06c06-166">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="06c06-166">Service lifetimes</span></span>

<span data-ttu-id="06c06-167">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="06c06-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="06c06-168">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="06c06-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="06c06-169">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-169">Transient</span></span>

<span data-ttu-id="06c06-170">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="06c06-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="06c06-171">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="06c06-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="06c06-172">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="06c06-173">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="06c06-173">Scoped</span></span>

<span data-ttu-id="06c06-174">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="06c06-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="06c06-175">Cuando se usa Entity Framework Core, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> registra tipos de `DbContext` con una duración de ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="06c06-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="06c06-176">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="06c06-177">Use servicios con ámbito en middleware con uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="06c06-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="06c06-178">Inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="06c06-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="06c06-179">Insertar a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) produce una excepción en tiempo de ejecución porque hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="06c06-180">El ejemplo de las [opciones de duración y de registro](#lifetime-and-registration-options) usa el enfoque `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="06c06-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="06c06-181">[Middleware basado en Factory](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="06c06-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="06c06-182">Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="06c06-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="06c06-183">Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones.</span><span class="sxs-lookup"><span data-stu-id="06c06-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="06c06-184">El middleware se registra como un servicio con ámbito o transitorio en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="06c06-185">Para más información, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="06c06-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="06c06-186">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="06c06-187">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="06c06-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="06c06-188">Basta con:</span><span class="sxs-lookup"><span data-stu-id="06c06-188">It's fine to:</span></span>

* <span data-ttu-id="06c06-189">Resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="06c06-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="06c06-190">Resolver un servicio con ámbito desde otro servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="06c06-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="06c06-191">Por defecto, en el entorno de desarrollo, resolver un servicio desde otro servicio con una duración más larga producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="06c06-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="06c06-192">Para más información, vea [Validación del ámbito](#sv).</span><span class="sxs-lookup"><span data-stu-id="06c06-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="06c06-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-193">Singleton</span></span>

<span data-ttu-id="06c06-194">Los servicios de duración de singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean de alguna de las siguientes formas:</span><span class="sxs-lookup"><span data-stu-id="06c06-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="06c06-195">La primera vez que se solicitan.</span><span class="sxs-lookup"><span data-stu-id="06c06-195">The first time they're requested.</span></span>
* <span data-ttu-id="06c06-196">Mediante el desarrollador, al proporcionar una instancia de implementación directamente al contenedor.</span><span class="sxs-lookup"><span data-stu-id="06c06-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="06c06-197">Este enfoque rara vez es necesario.</span><span class="sxs-lookup"><span data-stu-id="06c06-197">This approach is rarely needed.</span></span>

<span data-ttu-id="06c06-198">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="06c06-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="06c06-199">Si la aplicación requiere un comportamiento de singleton, permita que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="06c06-200">No implemente el modelo de diseño singleton y proporcione el código para desechar el singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="06c06-201">Los servicios nunca deben desecharse mediante el código que haya resuelto el servicio de un contenedor.</span><span class="sxs-lookup"><span data-stu-id="06c06-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="06c06-202">Si un tipo o generador se registra como singleton, el contenedor eliminará el singleton automáticamente.</span><span class="sxs-lookup"><span data-stu-id="06c06-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="06c06-203">Los servicios singleton deben ser seguros para los subprocesos y se suelen usar en servicios sin estado.</span><span class="sxs-lookup"><span data-stu-id="06c06-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="06c06-204">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="06c06-205">Dado que no se libera memoria hasta que se apaga la aplicación, se debe tener en cuenta el uso de memoria con un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="06c06-206">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="06c06-207">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="06c06-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="06c06-208">Basta con resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="06c06-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="06c06-209">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="06c06-209">Service registration methods</span></span>

<span data-ttu-id="06c06-210">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="06c06-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="06c06-211">Método</span><span class="sxs-lookup"><span data-stu-id="06c06-211">Method</span></span> | <span data-ttu-id="06c06-212">Automático</span><span class="sxs-lookup"><span data-stu-id="06c06-212">Automatic</span></span><br><span data-ttu-id="06c06-213">objeto</span><span class="sxs-lookup"><span data-stu-id="06c06-213">object</span></span><br><span data-ttu-id="06c06-214">eliminación</span><span class="sxs-lookup"><span data-stu-id="06c06-214">disposal</span></span> | <span data-ttu-id="06c06-215">Múltiple</span><span class="sxs-lookup"><span data-stu-id="06c06-215">Multiple</span></span><br><span data-ttu-id="06c06-216">implementaciones</span><span class="sxs-lookup"><span data-stu-id="06c06-216">implementations</span></span> | <span data-ttu-id="06c06-217">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="06c06-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="06c06-218">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="06c06-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="06c06-219">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-219">Yes</span></span> | <span data-ttu-id="06c06-220">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-220">Yes</span></span> | <span data-ttu-id="06c06-221">No</span><span class="sxs-lookup"><span data-stu-id="06c06-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-222">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="06c06-223">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-223">Yes</span></span> | <span data-ttu-id="06c06-224">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-224">Yes</span></span> | <span data-ttu-id="06c06-225">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="06c06-226">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="06c06-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="06c06-227">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-227">Yes</span></span> | <span data-ttu-id="06c06-228">No</span><span class="sxs-lookup"><span data-stu-id="06c06-228">No</span></span> | <span data-ttu-id="06c06-229">No</span><span class="sxs-lookup"><span data-stu-id="06c06-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-230">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="06c06-231">No</span><span class="sxs-lookup"><span data-stu-id="06c06-231">No</span></span> | <span data-ttu-id="06c06-232">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-232">Yes</span></span> | <span data-ttu-id="06c06-233">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-234">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="06c06-235">No</span><span class="sxs-lookup"><span data-stu-id="06c06-235">No</span></span> | <span data-ttu-id="06c06-236">No</span><span class="sxs-lookup"><span data-stu-id="06c06-236">No</span></span> | <span data-ttu-id="06c06-237">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-237">Yes</span></span> |

<span data-ttu-id="06c06-238">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="06c06-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="06c06-239">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="06c06-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="06c06-240">Los métodos `TryAdd{LIFETIME}` registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="06c06-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="06c06-241">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="06c06-242">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="06c06-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="06c06-243">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="06c06-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="06c06-244">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="06c06-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="06c06-245">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="06c06-246">Al registrar los servicios, el desarrollador debería agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="06c06-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="06c06-247">Por lo general, los autores de bibliotecas usan `TryAddEnumerable` para evitar el registro de varias copias de una implementación en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="06c06-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="06c06-248">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="06c06-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="06c06-249">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="06c06-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="06c06-250">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="06c06-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="06c06-251">Normalmente, el registro del servicio es independiente, excepto cuando se registran varias implementaciones del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="06c06-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="06c06-252">`IServiceCollection` es una colección de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="06c06-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="06c06-253">En el código siguiente se muestra cómo agregar un servicio con un constructor:</span><span class="sxs-lookup"><span data-stu-id="06c06-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="06c06-254">Los métodos `Add{LIFETIME}` usan el mismo enfoque.</span><span class="sxs-lookup"><span data-stu-id="06c06-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="06c06-255">Por ejemplo, vea el [código fuente de AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="06c06-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="06c06-256">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="06c06-256">Constructor injection behavior</span></span>

<span data-ttu-id="06c06-257">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="06c06-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="06c06-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="06c06-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="06c06-259">Permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="06c06-260">Se utiliza con características de marcos, como [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), controladores MVC y [enlazadores de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="06c06-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="06c06-261">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="06c06-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="06c06-262">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="06c06-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="06c06-263">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="06c06-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="06c06-264">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="06c06-265">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="06c06-265">Entity Framework contexts</span></span>

<span data-ttu-id="06c06-266">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="06c06-267">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="06c06-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="06c06-268">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="06c06-269">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="06c06-269">Lifetime and registration options</span></span>

<span data-ttu-id="06c06-270">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="06c06-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="06c06-271">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="06c06-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="06c06-272">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="06c06-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="06c06-273">El constructor `Operation` genera los cuatro últimos caracteres de un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="06c06-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="06c06-274">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="06c06-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="06c06-275">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="06c06-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="06c06-276">El objeto `IndexModel` y el middleware de la aplicación de ejemplo solicita cada tipo de `IOperation` y registra la `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="06c06-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="06c06-277">El middleware es similar al `IndexModel` y resuelve los mismos servicios:</span><span class="sxs-lookup"><span data-stu-id="06c06-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="06c06-278">Los servicios con ámbito se deben resolver en el método `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="06c06-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="06c06-279">La salida del registrador muestra:</span><span class="sxs-lookup"><span data-stu-id="06c06-279">The logger output shows:</span></span>

* <span data-ttu-id="06c06-280">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="06c06-280">*Transient* objects are always different.</span></span> <span data-ttu-id="06c06-281">El valor transitorio de `OperationId` es diferente en el `IndexModel` y el middleware.</span><span class="sxs-lookup"><span data-stu-id="06c06-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="06c06-282">Los objetos *con ámbito* son iguales dentro de una solicitud, pero varían entre solicitudes.</span><span class="sxs-lookup"><span data-stu-id="06c06-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="06c06-283">Los objetos *singleton* son los mismos para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="06c06-284">Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="06c06-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="06c06-285">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="06c06-285">Call services from main</span></span>

<span data-ttu-id="06c06-286">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="06c06-287">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización:</span><span class="sxs-lookup"><span data-stu-id="06c06-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="06c06-288">El código anterior procede de [Agregar el inicializador](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) en el tutorial de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="06c06-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="06c06-289">En el siguiente ejemplo se indica cómo obtener un contexto para `IMyDependency` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="06c06-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="06c06-290">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="06c06-290">Scope validation</span></span>

<span data-ttu-id="06c06-291">Cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments) y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06c06-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="06c06-292">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="06c06-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="06c06-293">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="06c06-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="06c06-294">Los servicios transitorios no se insertan directa o indirectamente en singletons o servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="06c06-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="06c06-295">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="06c06-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="06c06-296">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="06c06-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="06c06-297">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="06c06-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="06c06-298">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="06c06-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="06c06-299">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="06c06-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="06c06-300">Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="06c06-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="06c06-301">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="06c06-301">Request Services</span></span>

<span data-ttu-id="06c06-302">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="06c06-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="06c06-303">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="06c06-304">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="06c06-305">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="06c06-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="06c06-306">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="06c06-307">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="06c06-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="06c06-308">ASP.NET Core crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios de ámbito.</span><span class="sxs-lookup"><span data-stu-id="06c06-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="06c06-309">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="06c06-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="06c06-310">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="06c06-311">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="06c06-311">Design services for dependency injection</span></span>

<span data-ttu-id="06c06-312">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="06c06-312">Best practices are to:</span></span>

* <span data-ttu-id="06c06-313">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="06c06-314">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="06c06-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="06c06-315">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="06c06-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="06c06-316">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="06c06-317">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="06c06-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="06c06-318">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="06c06-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="06c06-319">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="06c06-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="06c06-320">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="06c06-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="06c06-321">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="06c06-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="06c06-322">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="06c06-322">Disposal of services</span></span>

<span data-ttu-id="06c06-323">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="06c06-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="06c06-324">Los servicios nunca deben desecharse mediante ningún código que haya resuelto el servicio de un contenedor.</span><span class="sxs-lookup"><span data-stu-id="06c06-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="06c06-325">Si un tipo o generador se registra como singleton, el contenedor elimina el singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="06c06-326">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="06c06-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="06c06-327">La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:</span><span class="sxs-lookup"><span data-stu-id="06c06-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="06c06-328">Servicios no creados por el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="06c06-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="06c06-329">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="06c06-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="06c06-330">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="06c06-330">In the preceding code:</span></span>

* <span data-ttu-id="06c06-331">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="06c06-332">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="06c06-333">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="06c06-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="06c06-334">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="06c06-335">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="06c06-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="06c06-336">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="06c06-336">Transient, limited lifetime</span></span>

<span data-ttu-id="06c06-337">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="06c06-337">**Scenario**</span></span>

<span data-ttu-id="06c06-338">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="06c06-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="06c06-339">La instancia se resuelve en el ámbito raíz (contenedor raíz).</span><span class="sxs-lookup"><span data-stu-id="06c06-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="06c06-340">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="06c06-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="06c06-341">**Solución**</span><span class="sxs-lookup"><span data-stu-id="06c06-341">**Solution**</span></span>

<span data-ttu-id="06c06-342">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="06c06-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="06c06-343">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="06c06-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="06c06-344">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="06c06-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="06c06-345">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="06c06-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="06c06-346">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="06c06-347">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="06c06-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="06c06-348">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="06c06-348">**Scenario**</span></span>

<span data-ttu-id="06c06-349">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="06c06-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="06c06-350">**Solución**</span><span class="sxs-lookup"><span data-stu-id="06c06-350">**Solution**</span></span>

<span data-ttu-id="06c06-351">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="06c06-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="06c06-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="06c06-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="06c06-353">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="06c06-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="06c06-354">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="06c06-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="06c06-355">Instrucciones generales de IDisposable</span><span class="sxs-lookup"><span data-stu-id="06c06-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="06c06-356">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="06c06-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="06c06-357">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="06c06-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="06c06-358">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="06c06-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="06c06-359">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="06c06-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="06c06-360">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="06c06-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="06c06-361">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="06c06-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="06c06-362">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="06c06-363">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="06c06-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="06c06-364">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="06c06-364">Default service container replacement</span></span>

<span data-ttu-id="06c06-365">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="06c06-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="06c06-366">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="06c06-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="06c06-367">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="06c06-367">Property injection</span></span>
* <span data-ttu-id="06c06-368">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="06c06-368">Injection based on name</span></span>
* <span data-ttu-id="06c06-369">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="06c06-369">Child containers</span></span>
* <span data-ttu-id="06c06-370">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="06c06-370">Custom lifetime management</span></span>
* <span data-ttu-id="06c06-371">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="06c06-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="06c06-372">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="06c06-372">Convention-based registration</span></span>

<span data-ttu-id="06c06-373">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="06c06-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="06c06-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="06c06-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="06c06-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="06c06-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="06c06-376">Grace</span><span class="sxs-lookup"><span data-stu-id="06c06-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="06c06-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="06c06-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="06c06-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="06c06-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="06c06-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="06c06-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="06c06-380">Unity</span><span class="sxs-lookup"><span data-stu-id="06c06-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="06c06-381">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="06c06-381">Thread safety</span></span>

<span data-ttu-id="06c06-382">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="06c06-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="06c06-383">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="06c06-384">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="06c06-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="06c06-385">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="06c06-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="06c06-386">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="06c06-386">Recommendations</span></span>

* <span data-ttu-id="06c06-387">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="06c06-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="06c06-388">C# no admite constructores asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="06c06-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="06c06-389">El patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="06c06-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="06c06-390">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="06c06-391">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="06c06-392">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="06c06-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="06c06-393">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="06c06-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="06c06-394">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="06c06-395">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-395">Avoid static access to services.</span></span> <span data-ttu-id="06c06-396">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="06c06-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="06c06-397">Mantenga los generadores de DI rápidos y sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="06c06-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="06c06-398">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="06c06-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="06c06-399">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="06c06-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="06c06-400">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="06c06-400">**Incorrect:**</span></span>

    ![Código incorrecto](dependency-injection/_static/bad.png)

  <span data-ttu-id="06c06-402">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="06c06-402">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="06c06-403">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="06c06-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="06c06-404">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="06c06-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="06c06-405">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="06c06-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="06c06-406">Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="06c06-407">La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="06c06-408">Por ejemplo, considere el caso en el que necesita obtener el `LoginPath` de la configuración.</span><span class="sxs-lookup"><span data-stu-id="06c06-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="06c06-409">Agregue el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="06c06-409">Avoid the following code:</span></span>

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="06c06-411">En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:</span><span class="sxs-lookup"><span data-stu-id="06c06-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="06c06-412">ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="06c06-413">Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".</span><span class="sxs-lookup"><span data-stu-id="06c06-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="06c06-414">La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores.</span><span class="sxs-lookup"><span data-stu-id="06c06-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="06c06-415">Una manera correcta de obtener `LoginPath` es usar el patrón de opción con DI:</span><span class="sxs-lookup"><span data-stu-id="06c06-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="06c06-416">El contenedor captura a los servicios transitorios descartables para su eliminación.</span><span class="sxs-lookup"><span data-stu-id="06c06-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="06c06-417">Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="06c06-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="06c06-418">Habilite la validación con ámbito para asegurarse de que la aplicación no tenga servicios con ámbito que capturen los singletons.</span><span class="sxs-lookup"><span data-stu-id="06c06-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="06c06-419">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="06c06-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="06c06-420">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="06c06-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="06c06-421">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="06c06-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="06c06-422">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="06c06-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="06c06-423">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="06c06-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="06c06-424">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="06c06-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="06c06-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino.</span><span class="sxs-lookup"><span data-stu-id="06c06-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="06c06-426">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="06c06-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="06c06-427">Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="06c06-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="06c06-428">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="06c06-428">Framework-provided services</span></span>

<span data-ttu-id="06c06-429">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="06c06-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="06c06-430">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="06c06-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="06c06-431">Las aplicaciones basadas en una plantilla de ASP.NET Core tienen más de 250 servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="06c06-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="06c06-432">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="06c06-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="06c06-433">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="06c06-433">Service Type</span></span> | <span data-ttu-id="06c06-434">Período de duración</span><span class="sxs-lookup"><span data-stu-id="06c06-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="06c06-435">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="06c06-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="06c06-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="06c06-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="06c06-439">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="06c06-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="06c06-441">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="06c06-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="06c06-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="06c06-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="06c06-445">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="06c06-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="06c06-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="06c06-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="06c06-449">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="06c06-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="06c06-450">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06c06-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="06c06-451">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="06c06-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="06c06-452">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="06c06-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="06c06-453">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="06c06-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="06c06-454">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06c06-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06c06-455">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="06c06-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="06c06-456">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="06c06-457">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="06c06-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="06c06-458">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06c06-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="06c06-459">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="06c06-459">Overview of dependency injection</span></span>

<span data-ttu-id="06c06-460">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="06c06-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="06c06-461">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="06c06-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="06c06-462">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="06c06-463">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="06c06-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="06c06-464">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="06c06-465">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="06c06-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="06c06-466">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="06c06-467">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="06c06-468">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="06c06-469">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="06c06-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="06c06-470">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="06c06-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="06c06-471">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="06c06-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="06c06-472">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="06c06-473">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="06c06-474">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="06c06-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="06c06-475">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="06c06-476">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="06c06-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="06c06-477">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="06c06-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="06c06-478">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="06c06-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="06c06-479">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="06c06-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="06c06-480">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="06c06-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="06c06-481">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="06c06-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="06c06-482">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="06c06-483">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="06c06-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="06c06-484">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="06c06-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="06c06-485">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="06c06-486">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="06c06-487">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="06c06-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="06c06-488">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="06c06-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="06c06-489">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="06c06-490">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="06c06-491">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="06c06-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="06c06-492">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="06c06-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="06c06-493">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="06c06-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="06c06-494">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="06c06-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="06c06-495">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="06c06-496">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="06c06-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="06c06-497">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="06c06-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="06c06-498">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="06c06-499">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="06c06-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="06c06-500">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="06c06-500">Services injected into Startup</span></span>

<span data-ttu-id="06c06-501">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="06c06-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="06c06-502">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="06c06-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="06c06-503">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="06c06-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="06c06-504">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="06c06-504">Framework-provided services</span></span>

<span data-ttu-id="06c06-505">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="06c06-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="06c06-506">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="06c06-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="06c06-507">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="06c06-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="06c06-508">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="06c06-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="06c06-509">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="06c06-509">Service Type</span></span> | <span data-ttu-id="06c06-510">Período de duración</span><span class="sxs-lookup"><span data-stu-id="06c06-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="06c06-511">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="06c06-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="06c06-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="06c06-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="06c06-515">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="06c06-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="06c06-517">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="06c06-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="06c06-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="06c06-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="06c06-521">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="06c06-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="06c06-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="06c06-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="06c06-525">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="06c06-525">Register additional services with extension methods</span></span>

<span data-ttu-id="06c06-526">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="06c06-527">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="06c06-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="06c06-528">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="06c06-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="06c06-529">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="06c06-529">Service lifetimes</span></span>

<span data-ttu-id="06c06-530">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="06c06-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="06c06-531">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="06c06-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="06c06-532">Transitorio</span><span class="sxs-lookup"><span data-stu-id="06c06-532">Transient</span></span>

<span data-ttu-id="06c06-533">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="06c06-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="06c06-534">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="06c06-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="06c06-535">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="06c06-536">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="06c06-536">Scoped</span></span>

<span data-ttu-id="06c06-537">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="06c06-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="06c06-538">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="06c06-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="06c06-539">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="06c06-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="06c06-540">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="06c06-541">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="06c06-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="06c06-542">Singleton</span><span class="sxs-lookup"><span data-stu-id="06c06-542">Singleton</span></span>

<span data-ttu-id="06c06-543">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="06c06-544">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="06c06-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="06c06-545">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="06c06-546">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="06c06-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="06c06-547">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="06c06-548">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="06c06-549">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="06c06-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="06c06-550">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="06c06-550">Service registration methods</span></span>

<span data-ttu-id="06c06-551">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="06c06-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="06c06-552">Método</span><span class="sxs-lookup"><span data-stu-id="06c06-552">Method</span></span> | <span data-ttu-id="06c06-553">Automático</span><span class="sxs-lookup"><span data-stu-id="06c06-553">Automatic</span></span><br><span data-ttu-id="06c06-554">objeto</span><span class="sxs-lookup"><span data-stu-id="06c06-554">object</span></span><br><span data-ttu-id="06c06-555">eliminación</span><span class="sxs-lookup"><span data-stu-id="06c06-555">disposal</span></span> | <span data-ttu-id="06c06-556">Múltiple</span><span class="sxs-lookup"><span data-stu-id="06c06-556">Multiple</span></span><br><span data-ttu-id="06c06-557">implementaciones</span><span class="sxs-lookup"><span data-stu-id="06c06-557">implementations</span></span> | <span data-ttu-id="06c06-558">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="06c06-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="06c06-559">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="06c06-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="06c06-560">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-560">Yes</span></span> | <span data-ttu-id="06c06-561">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-561">Yes</span></span> | <span data-ttu-id="06c06-562">No</span><span class="sxs-lookup"><span data-stu-id="06c06-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-563">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="06c06-564">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-564">Yes</span></span> | <span data-ttu-id="06c06-565">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-565">Yes</span></span> | <span data-ttu-id="06c06-566">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="06c06-567">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="06c06-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="06c06-568">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-568">Yes</span></span> | <span data-ttu-id="06c06-569">No</span><span class="sxs-lookup"><span data-stu-id="06c06-569">No</span></span> | <span data-ttu-id="06c06-570">No</span><span class="sxs-lookup"><span data-stu-id="06c06-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-571">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="06c06-572">No</span><span class="sxs-lookup"><span data-stu-id="06c06-572">No</span></span> | <span data-ttu-id="06c06-573">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-573">Yes</span></span> | <span data-ttu-id="06c06-574">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="06c06-575">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="06c06-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="06c06-576">No</span><span class="sxs-lookup"><span data-stu-id="06c06-576">No</span></span> | <span data-ttu-id="06c06-577">No</span><span class="sxs-lookup"><span data-stu-id="06c06-577">No</span></span> | <span data-ttu-id="06c06-578">Sí</span><span class="sxs-lookup"><span data-stu-id="06c06-578">Yes</span></span> |

<span data-ttu-id="06c06-579">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="06c06-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="06c06-580">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="06c06-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="06c06-581">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="06c06-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="06c06-582">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="06c06-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="06c06-583">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="06c06-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="06c06-584">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="06c06-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="06c06-585">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="06c06-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="06c06-586">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="06c06-587">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="06c06-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="06c06-588">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="06c06-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="06c06-589">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="06c06-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="06c06-590">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="06c06-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="06c06-591">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="06c06-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="06c06-592">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="06c06-592">Constructor injection behavior</span></span>

<span data-ttu-id="06c06-593">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="06c06-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="06c06-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="06c06-595">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="06c06-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="06c06-596">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="06c06-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="06c06-597">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="06c06-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="06c06-598">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="06c06-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="06c06-599">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="06c06-600">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="06c06-600">Entity Framework contexts</span></span>

<span data-ttu-id="06c06-601">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="06c06-602">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="06c06-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="06c06-603">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="06c06-604">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="06c06-604">Lifetime and registration options</span></span>

<span data-ttu-id="06c06-605">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="06c06-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="06c06-606">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="06c06-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="06c06-607">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="06c06-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="06c06-608">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="06c06-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="06c06-609">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="06c06-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="06c06-610">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="06c06-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="06c06-611">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="06c06-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="06c06-612">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="06c06-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="06c06-613">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="06c06-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="06c06-614">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="06c06-615">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="06c06-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="06c06-616">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="06c06-617">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="06c06-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="06c06-618">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="06c06-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="06c06-619">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="06c06-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="06c06-620">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="06c06-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="06c06-621">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="06c06-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="06c06-622">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="06c06-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="06c06-623">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="06c06-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="06c06-624">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="06c06-624">**First request:**</span></span>

<span data-ttu-id="06c06-625">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="06c06-625">Controller operations:</span></span>

<span data-ttu-id="06c06-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="06c06-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="06c06-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="06c06-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="06c06-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="06c06-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="06c06-629">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="06c06-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="06c06-630">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="06c06-630">`OperationService` operations:</span></span>

<span data-ttu-id="06c06-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="06c06-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="06c06-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="06c06-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="06c06-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="06c06-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="06c06-634">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="06c06-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="06c06-635">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="06c06-635">**Second request:**</span></span>

<span data-ttu-id="06c06-636">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="06c06-636">Controller operations:</span></span>

<span data-ttu-id="06c06-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="06c06-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="06c06-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="06c06-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="06c06-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="06c06-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="06c06-640">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="06c06-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="06c06-641">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="06c06-641">`OperationService` operations:</span></span>

<span data-ttu-id="06c06-642">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="06c06-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="06c06-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="06c06-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="06c06-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="06c06-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="06c06-645">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="06c06-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="06c06-646">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="06c06-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="06c06-647">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="06c06-647">*Transient* objects are always different.</span></span> <span data-ttu-id="06c06-648">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="06c06-649">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="06c06-650">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="06c06-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="06c06-651">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="06c06-652">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="06c06-652">Call services from main</span></span>

<span data-ttu-id="06c06-653">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="06c06-654">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="06c06-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="06c06-655">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="06c06-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="06c06-656">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="06c06-656">Scope validation</span></span>

<span data-ttu-id="06c06-657">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06c06-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="06c06-658">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="06c06-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="06c06-659">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="06c06-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="06c06-660">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="06c06-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="06c06-661">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="06c06-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="06c06-662">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="06c06-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="06c06-663">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="06c06-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="06c06-664">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="06c06-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="06c06-665">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="06c06-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="06c06-666">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="06c06-666">Request Services</span></span>

<span data-ttu-id="06c06-667">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="06c06-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="06c06-668">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="06c06-669">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="06c06-670">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="06c06-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="06c06-671">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="06c06-672">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="06c06-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="06c06-673">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="06c06-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="06c06-674">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="06c06-674">Design services for dependency injection</span></span>

<span data-ttu-id="06c06-675">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="06c06-675">Best practices are to:</span></span>

* <span data-ttu-id="06c06-676">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="06c06-677">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="06c06-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="06c06-678">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="06c06-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="06c06-679">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="06c06-680">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="06c06-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="06c06-681">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="06c06-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="06c06-682">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="06c06-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="06c06-683">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="06c06-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="06c06-684">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="06c06-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="06c06-685">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="06c06-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="06c06-686">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="06c06-686">Disposal of services</span></span>

<span data-ttu-id="06c06-687">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="06c06-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="06c06-688">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="06c06-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="06c06-689">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="06c06-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="06c06-690">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="06c06-690">In the following example:</span></span>

* <span data-ttu-id="06c06-691">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="06c06-692">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="06c06-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="06c06-693">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="06c06-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="06c06-694">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="06c06-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="06c06-695">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="06c06-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="06c06-696">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="06c06-696">Transient, limited lifetime</span></span>

<span data-ttu-id="06c06-697">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="06c06-697">**Scenario**</span></span>

<span data-ttu-id="06c06-698">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="06c06-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="06c06-699">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="06c06-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="06c06-700">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="06c06-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="06c06-701">**Solución**</span><span class="sxs-lookup"><span data-stu-id="06c06-701">**Solution**</span></span>

<span data-ttu-id="06c06-702">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="06c06-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="06c06-703">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="06c06-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="06c06-704">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="06c06-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="06c06-705">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="06c06-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="06c06-706">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="06c06-707">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="06c06-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="06c06-708">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="06c06-708">**Scenario**</span></span>

<span data-ttu-id="06c06-709">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="06c06-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="06c06-710">**Solución**</span><span class="sxs-lookup"><span data-stu-id="06c06-710">**Solution**</span></span>

<span data-ttu-id="06c06-711">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="06c06-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="06c06-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="06c06-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="06c06-713">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="06c06-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="06c06-714">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="06c06-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="06c06-715">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="06c06-715">General Guidelines</span></span>

* <span data-ttu-id="06c06-716">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="06c06-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="06c06-717">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="06c06-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="06c06-718">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="06c06-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="06c06-719">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="06c06-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="06c06-720">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="06c06-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="06c06-721">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="06c06-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="06c06-722">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="06c06-723">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="06c06-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="06c06-724">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="06c06-724">Default service container replacement</span></span>

<span data-ttu-id="06c06-725">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="06c06-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="06c06-726">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="06c06-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="06c06-727">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="06c06-727">Property injection</span></span>
* <span data-ttu-id="06c06-728">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="06c06-728">Injection based on name</span></span>
* <span data-ttu-id="06c06-729">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="06c06-729">Child containers</span></span>
* <span data-ttu-id="06c06-730">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="06c06-730">Custom lifetime management</span></span>
* <span data-ttu-id="06c06-731">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="06c06-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="06c06-732">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="06c06-732">Convention-based registration</span></span>

<span data-ttu-id="06c06-733">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="06c06-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="06c06-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="06c06-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="06c06-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="06c06-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="06c06-736">Grace</span><span class="sxs-lookup"><span data-stu-id="06c06-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="06c06-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="06c06-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="06c06-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="06c06-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="06c06-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="06c06-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="06c06-740">Unity</span><span class="sxs-lookup"><span data-stu-id="06c06-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="06c06-741">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="06c06-741">Thread safety</span></span>

<span data-ttu-id="06c06-742">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="06c06-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="06c06-743">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="06c06-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="06c06-744">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="06c06-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="06c06-745">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="06c06-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="06c06-746">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="06c06-746">Recommendations</span></span>

* <span data-ttu-id="06c06-747">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="06c06-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="06c06-748">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="06c06-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="06c06-749">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="06c06-750">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="06c06-751">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="06c06-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="06c06-752">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="06c06-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="06c06-753">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="06c06-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="06c06-754">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="06c06-754">Avoid static access to services.</span></span> <span data-ttu-id="06c06-755">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="06c06-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="06c06-756">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="06c06-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="06c06-757">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="06c06-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="06c06-758">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="06c06-758">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="06c06-759">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="06c06-759">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="06c06-760">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="06c06-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="06c06-761">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="06c06-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="06c06-762">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="06c06-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="06c06-763">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="06c06-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="06c06-764">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="06c06-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="06c06-765">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="06c06-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06c06-766">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="06c06-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="06c06-767">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06c06-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="06c06-768">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="06c06-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="06c06-769">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="06c06-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="06c06-770">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="06c06-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="06c06-771">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06c06-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
