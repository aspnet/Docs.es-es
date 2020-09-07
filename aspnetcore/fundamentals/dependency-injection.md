---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153550"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="c2695-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2695-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2695-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c2695-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c2695-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c2695-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c2695-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c2695-107">Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="c2695-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c2695-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2695-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c2695-109">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-109">Overview of dependency injection</span></span>

<span data-ttu-id="c2695-110">Una *dependencia* es un objeto del que depende otro objeto.</span><span class="sxs-lookup"><span data-stu-id="c2695-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="c2695-111">Examine la clase `MyDependency` siguiente con un método `WriteMessage` del que dependen otras clases:</span><span class="sxs-lookup"><span data-stu-id="c2695-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="c2695-112">Una clase puede crear una instancia de la clase `MyDependency` para usar su método `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="c2695-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="c2695-113">En el ejemplo siguiente, la clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="c2695-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="c2695-114">La clase crea y depende directamente de la clase `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="c2695-115">Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las razones siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c2695-116">Para reemplazar `MyDependency` por una implementación diferente, se debe modificar la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c2695-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="c2695-117">Si `MyDependency` tiene dependencias, deben configurarse según la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c2695-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="c2695-118">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c2695-119">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="c2695-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c2695-120">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="c2695-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c2695-121">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="c2695-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c2695-122">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c2695-123">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c2695-124">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="c2695-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c2695-125">Por lo general, los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c2695-126">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="c2695-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c2695-127">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="c2695-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c2695-128">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define el método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="c2695-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c2695-129">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="c2695-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c2695-130">La aplicación de ejemplo registra el servicio `IMyDependency` con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c2695-131">El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="c2695-132">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="c2695-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="c2695-133">En la aplicación de ejemplo, el servicio `IMyDependency` se solicita y usa para llamar al método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="c2695-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="c2695-134">Mediante el uso del patrón de DI, el controlador:</span><span class="sxs-lookup"><span data-stu-id="c2695-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="c2695-135">No usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency` que implementa.</span><span class="sxs-lookup"><span data-stu-id="c2695-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="c2695-136">Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.</span><span class="sxs-lookup"><span data-stu-id="c2695-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="c2695-137">No crea una instancia de `MyDependency`, la crea el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="c2695-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="c2695-138">La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:</span><span class="sxs-lookup"><span data-stu-id="c2695-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="c2695-139">El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="c2695-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="c2695-140">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601>, el que solicita en el constructor.</span><span class="sxs-lookup"><span data-stu-id="c2695-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="c2695-141">`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="c2695-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="c2695-142">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="c2695-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c2695-143">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c2695-144">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="c2695-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c2695-145">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="c2695-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c2695-146">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="c2695-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="c2695-147">En la terminología de la inserción de dependencias, un servicio:</span><span class="sxs-lookup"><span data-stu-id="c2695-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="c2695-148">Por lo general, es un objeto que proporciona un servicio a otros objetos, como el servicio `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="c2695-149">No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.</span><span class="sxs-lookup"><span data-stu-id="c2695-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="c2695-150">El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido.</span><span class="sxs-lookup"><span data-stu-id="c2695-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="c2695-151">Las implementaciones de `IMyDependency` que se muestran en los ejemplos anteriores se escribieron para mostrar la inserción de DI básica, no para implementar el registro.</span><span class="sxs-lookup"><span data-stu-id="c2695-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="c2695-152">La mayoría de las aplicaciones no deberían tener que escribir registradores.</span><span class="sxs-lookup"><span data-stu-id="c2695-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="c2695-153">En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c2695-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="c2695-154">Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="c2695-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="c2695-155">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="c2695-155">Services injected into Startup</span></span>

<span data-ttu-id="c2695-156">Los servicios se pueden insertar en el constructor `Startup` y en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c2695-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="c2695-157">Solo se pueden insertar los servicios siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="c2695-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c2695-158">Cualquier servicio registrado con el contenedor de DI se puede insertar en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c2695-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="c2695-159">Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="c2695-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="c2695-160">Registro de grupos de servicios con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="c2695-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="c2695-161">El marco ASP.NET Core usa una convención para registrar un grupo de servicios relacionados.</span><span class="sxs-lookup"><span data-stu-id="c2695-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="c2695-162">La convención es usar un único método de extensión de `Add{GROUP_NAME}` para registrar todos los servicios requeridos por una característica de marco.</span><span class="sxs-lookup"><span data-stu-id="c2695-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="c2695-163">Por ejemplo, el método de extensión <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> registra los servicios necesarios para los controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="c2695-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="c2695-164">El código siguiente lo genera la plantilla de Razor Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="c2695-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="c2695-165">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="c2695-165">Service lifetimes</span></span>

<span data-ttu-id="c2695-166">Los servicios se pueden registrar con una de las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="c2695-167">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-167">Transient</span></span>
* <span data-ttu-id="c2695-168">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="c2695-168">Scoped</span></span>
* <span data-ttu-id="c2695-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-169">Singleton</span></span>

<span data-ttu-id="c2695-170">En las secciones siguientes se describen cada una de las duraciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="c2695-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="c2695-171">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="c2695-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="c2695-172">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-172">Transient</span></span>

<span data-ttu-id="c2695-173">Los servicios de duración transitoria se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="c2695-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="c2695-174">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="c2695-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="c2695-175">Registre los servicios transitorios con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span><span class="sxs-lookup"><span data-stu-id="c2695-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="c2695-176">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c2695-177">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="c2695-177">Scoped</span></span>

<span data-ttu-id="c2695-178">Los servicios de duración con ámbito se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="c2695-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="c2695-179">Registre los servicios con ámbito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span><span class="sxs-lookup"><span data-stu-id="c2695-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="c2695-180">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="c2695-181">Cuando se usa Entity Framework Core, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> registra tipos de `DbContext` con una duración de ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c2695-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="c2695-182">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c2695-183">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="c2695-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c2695-184">Basta con:</span><span class="sxs-lookup"><span data-stu-id="c2695-184">It's fine to:</span></span>

* <span data-ttu-id="c2695-185">Resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="c2695-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="c2695-186">Resolver un servicio con ámbito desde otro servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="c2695-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="c2695-187">De manera predeterminada, en el entorno de desarrollo, resolver un servicio desde otro servicio con una duración más larga genera una excepción.</span><span class="sxs-lookup"><span data-stu-id="c2695-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="c2695-188">Para más información, vea [Validación del ámbito](#sv).</span><span class="sxs-lookup"><span data-stu-id="c2695-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="c2695-189">Para usar servicios con ámbito en middleware, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="c2695-190">Inserte el servicio en el método `Invoke` o `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="c2695-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c2695-191">El uso de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) genera una excepción en tiempo de ejecución porque obliga al servicio con ámbito a comportarse como un singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="c2695-192">El ejemplo que aparece en la sección [Opciones de registro y duración](#lifetime-and-registration-options) muestra el enfoque `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c2695-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="c2695-193">Use [middleware basado en Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="c2695-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="c2695-194">El middleware registrado mediante este enfoque se activa por solicitud de cliente (conexión), lo que permite que los servicios con ámbito se inserten en el método `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="c2695-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="c2695-195">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c2695-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c2695-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-196">Singleton</span></span>

<span data-ttu-id="c2695-197">Los servicios de duración de singleton se crean de alguna de las formas siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="c2695-198">La primera vez que se solicitan.</span><span class="sxs-lookup"><span data-stu-id="c2695-198">The first time they're requested.</span></span>
* <span data-ttu-id="c2695-199">Mediante el desarrollador, al proporcionar una instancia de implementación directamente al contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="c2695-200">Este enfoque rara vez es necesario.</span><span class="sxs-lookup"><span data-stu-id="c2695-200">This approach is rarely needed.</span></span>

<span data-ttu-id="c2695-201">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="c2695-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c2695-202">Si la aplicación requiere un comportamiento de singleton, permita que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="c2695-203">No implemente el modelo de diseño singleton y proporcione el código para desechar el singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="c2695-204">Los servicios nunca deben desecharse mediante el código que haya resuelto el servicio del contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="c2695-205">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="c2695-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="c2695-206">Registre los servicios singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="c2695-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="c2695-207">Los servicios singleton deben ser seguros para los subprocesos y se suelen usar en servicios sin estado.</span><span class="sxs-lookup"><span data-stu-id="c2695-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="c2695-208">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="c2695-209">Como no se libera memoria hasta que se apaga la aplicación, se debe tener en cuenta el uso de memoria con un servicio singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="c2695-210">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c2695-211">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="c2695-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c2695-212">Basta con resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="c2695-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c2695-213">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="c2695-213">Service registration methods</span></span>

<span data-ttu-id="c2695-214">El marco proporciona métodos de extensión de registro del servicio que resultan útiles en escenarios específicos:</span><span class="sxs-lookup"><span data-stu-id="c2695-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="c2695-215">Método</span><span class="sxs-lookup"><span data-stu-id="c2695-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="c2695-216">Automático</span><span class="sxs-lookup"><span data-stu-id="c2695-216">Automatic</span></span><br><span data-ttu-id="c2695-217">objeto</span><span class="sxs-lookup"><span data-stu-id="c2695-217">object</span></span><br><span data-ttu-id="c2695-218">eliminación</span><span class="sxs-lookup"><span data-stu-id="c2695-218">disposal</span></span> | <span data-ttu-id="c2695-219">Múltiple</span><span class="sxs-lookup"><span data-stu-id="c2695-219">Multiple</span></span><br><span data-ttu-id="c2695-220">implementaciones</span><span class="sxs-lookup"><span data-stu-id="c2695-220">implementations</span></span> | <span data-ttu-id="c2695-221">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="c2695-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c2695-222">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2695-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="c2695-223">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-223">Yes</span></span>                             | <span data-ttu-id="c2695-224">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-224">Yes</span></span>                         | <span data-ttu-id="c2695-225">No</span><span class="sxs-lookup"><span data-stu-id="c2695-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-226">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="c2695-227">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-227">Yes</span></span>                             | <span data-ttu-id="c2695-228">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-228">Yes</span></span>                         | <span data-ttu-id="c2695-229">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c2695-230">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2695-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="c2695-231">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-231">Yes</span></span>                             | <span data-ttu-id="c2695-232">No</span><span class="sxs-lookup"><span data-stu-id="c2695-232">No</span></span>                          | <span data-ttu-id="c2695-233">No</span><span class="sxs-lookup"><span data-stu-id="c2695-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-234">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="c2695-235">No</span><span class="sxs-lookup"><span data-stu-id="c2695-235">No</span></span>                              | <span data-ttu-id="c2695-236">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-236">Yes</span></span>                         | <span data-ttu-id="c2695-237">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-238">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="c2695-239">No</span><span class="sxs-lookup"><span data-stu-id="c2695-239">No</span></span>                              | <span data-ttu-id="c2695-240">No</span><span class="sxs-lookup"><span data-stu-id="c2695-240">No</span></span>                          | <span data-ttu-id="c2695-241">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-241">Yes</span></span>       |

<span data-ttu-id="c2695-242">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="c2695-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c2695-243">Es habitual usar varias implementaciones al [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="c2695-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c2695-244">El marco también proporciona métodos de extensión `TryAdd{LIFETIME}`, que registran el servicio solo si todavía no hay registrada una implementación.</span><span class="sxs-lookup"><span data-stu-id="c2695-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c2695-245">En el ejemplo siguiente, la llamada a `AddSingleton` registra `MyDependency` como una implementación para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="c2695-246">La llamada a `TryAddSingleton` no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="c2695-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c2695-247">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="c2695-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="c2695-248">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registran el servicio solo si todavía no hay una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="c2695-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c2695-249">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c2695-250">Al registrar los servicios, el desarrollador debería agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="c2695-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c2695-251">Por lo general, los autores de bibliotecas usan `TryAddEnumerable` para evitar el registro de varias copias de una implementación en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="c2695-252">En el ejemplo siguiente, la primera llamada a `TryAddEnumerable` registra `MyDependency` como una implementación para `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="c2695-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="c2695-253">La segunda llamada registra `MyDependency` para `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="c2695-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="c2695-254">La tercera llamada no tiene ningún efecto porque `IMyDependency1` ya tiene una implementación registrada de `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="c2695-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="c2695-255">Normalmente, el registro del servicio es independiente, excepto cuando se registran varias implementaciones del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="c2695-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="c2695-256">`IServiceCollection` es una colección de objetos <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="c2695-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="c2695-257">En el ejemplo siguiente se muestra cómo registrar un servicio mediante la creación e incorporación de un elemento `ServiceDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="c2695-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="c2695-258">Los métodos `Add{LIFETIME}` integrados usan el mismo enfoque.</span><span class="sxs-lookup"><span data-stu-id="c2695-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="c2695-259">Por ejemplo, consulte el [código fuente de AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="c2695-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c2695-260">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="c2695-260">Constructor injection behavior</span></span>

<span data-ttu-id="c2695-261">Los servicios se pueden resolver mediante el uso de:</span><span class="sxs-lookup"><span data-stu-id="c2695-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c2695-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="c2695-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="c2695-263">Crea objetos que no están registrados en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="c2695-264">Se utiliza con características de marcos, como [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), controladores MVC y [enlazadores de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c2695-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="c2695-265">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="c2695-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c2695-266">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="c2695-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c2695-267">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="c2695-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c2695-268">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c2695-269">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c2695-269">Entity Framework contexts</span></span>

<span data-ttu-id="c2695-270">De manera predeterminada, los contextos de Entity Framework se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c2695-271">Para usar una duración distinta, especifíquela mediante el uso de una sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="c2695-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="c2695-272">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que la del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c2695-273">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="c2695-273">Lifetime and registration options</span></span>

<span data-ttu-id="c2695-274">Para mostrar la diferencia entre las duraciones del servicio y sus opciones de registro, considere las interfaces siguientes que representan una tarea como una operación con un identificador, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c2695-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="c2695-275">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona las mismas instancias del servicio u otras distintas cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="c2695-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c2695-276">La clase `Operation` siguiente implementa todas las interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="c2695-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="c2695-277">El constructor `Operation` genera un GUID y almacena los 4 últimos caracteres en la propiedad `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="c2695-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="c2695-278">El método `Startup.ConfigureServices` crea varios registros de la clase `Operation` de acuerdo con las duraciones mencionadas:</span><span class="sxs-lookup"><span data-stu-id="c2695-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c2695-279">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="c2695-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="c2695-280">`IndexModel` y el middleware solicitan cada clase del tipo `IOperation` y registran el valor de `OperationId` de cada una:</span><span class="sxs-lookup"><span data-stu-id="c2695-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c2695-281">De manera similar al `IndexModel`, el middleware resuelve los mismos servicios:</span><span class="sxs-lookup"><span data-stu-id="c2695-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="c2695-282">Los servicios con ámbito se deben resolver en el método `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="c2695-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="c2695-283">La salida del registrador muestra:</span><span class="sxs-lookup"><span data-stu-id="c2695-283">The logger output shows:</span></span>

* <span data-ttu-id="c2695-284">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="c2695-284">*Transient* objects are always different.</span></span> <span data-ttu-id="c2695-285">El valor `OperationId` transitorio es distinto en el `IndexModel` y en el middleware.</span><span class="sxs-lookup"><span data-stu-id="c2695-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="c2695-286">Los objetos *con ámbito* son iguales para cada solicitud, pero varían entre solicitudes.</span><span class="sxs-lookup"><span data-stu-id="c2695-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="c2695-287">Los objetos *singleton* son los mismos para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="c2695-288">Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="c2695-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="c2695-289">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="c2695-289">Call services from main</span></span>

<span data-ttu-id="c2695-290">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c2695-291">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="c2695-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="c2695-292">En el ejemplo siguiente se muestra cómo acceder al servicio `IMyDependency` con ámbito y llamar a su método `WriteMessage` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c2695-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="c2695-293">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="c2695-293">Scope validation</span></span>

<span data-ttu-id="c2695-294">Cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments) y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2695-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c2695-295">Los servicios con ámbito no se resuelven desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="c2695-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="c2695-296">Los servicios con ámbito no se insertan en singletons.</span><span class="sxs-lookup"><span data-stu-id="c2695-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="c2695-297">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="c2695-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="c2695-298">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="c2695-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c2695-299">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="c2695-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c2695-300">Si un servicio con ámbito se crea en el contenedor raíz, su duración sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="c2695-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="c2695-301">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="c2695-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c2695-302">Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="c2695-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="c2695-303">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="c2695-303">Request Services</span></span>

<span data-ttu-id="c2695-304">Los servicios disponibles en una solicitud de ASP.NET Core se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="c2695-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="c2695-305">Cuando se solicitan servicios dentro de una solicitud, los servicios y sus dependencias se resuelven desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="c2695-306">El marco crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="c2695-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="c2695-307">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="c2695-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="c2695-308">Se recomienda que solicite las dependencias como parámetros del constructor para resolver los servicios desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="c2695-309">Esto genera clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="c2695-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c2695-310">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-310">Design services for dependency injection</span></span>

<span data-ttu-id="c2695-311">Al diseñar servicios para la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="c2695-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="c2695-312">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="c2695-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c2695-313">Evitar crear un estado global mediante el diseño de aplicaciones para usar servicios singleton en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c2695-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="c2695-314">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c2695-315">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="c2695-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c2695-316">Cree servicios pequeños, bien factorizados y probados con facilidad.</span><span class="sxs-lookup"><span data-stu-id="c2695-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c2695-317">Si una clase tiene muchas dependencias insertadas, podría ser un signo de que la clase tiene demasiadas responsabilidades e infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c2695-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c2695-318">Trate de mover algunas de las responsabilidades de la clase a clases nuevas para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="c2695-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="c2695-319">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c2695-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c2695-320">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="c2695-320">Disposal of services</span></span>

<span data-ttu-id="c2695-321">El contenedor llama a <xref:System.IDisposable.Dispose%2A> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="c2695-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c2695-322">El desarrollador nunca debe eliminar los servicios resueltos desde el contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="c2695-323">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="c2695-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="c2695-324">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="c2695-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="c2695-325">La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:</span><span class="sxs-lookup"><span data-stu-id="c2695-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="c2695-326">Servicios no creados por el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="c2695-326">Services not created by the service container</span></span>

<span data-ttu-id="c2695-327">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2695-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="c2695-328">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="c2695-328">In the preceding code:</span></span>

* <span data-ttu-id="c2695-329">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c2695-330">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="c2695-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c2695-331">El desarrollador es responsable de la eliminación de los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c2695-332">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="c2695-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c2695-333">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="c2695-333">Transient, limited lifetime</span></span>

<span data-ttu-id="c2695-334">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="c2695-334">**Scenario**</span></span>

<span data-ttu-id="c2695-335">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="c2695-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c2695-336">La instancia se resuelve en el ámbito raíz (contenedor raíz).</span><span class="sxs-lookup"><span data-stu-id="c2695-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="c2695-337">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="c2695-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c2695-338">**Solución**</span><span class="sxs-lookup"><span data-stu-id="c2695-338">**Solution**</span></span>

<span data-ttu-id="c2695-339">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="c2695-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c2695-340">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="c2695-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c2695-341">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="c2695-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c2695-342">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="c2695-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c2695-343">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c2695-344">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="c2695-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="c2695-345">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="c2695-345">**Scenario**</span></span>

<span data-ttu-id="c2695-346">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero la instancia de <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="c2695-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="c2695-347">**Solución**</span><span class="sxs-lookup"><span data-stu-id="c2695-347">**Solution**</span></span>

<span data-ttu-id="c2695-348">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="c2695-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="c2695-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="c2695-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c2695-350">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="c2695-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c2695-351">Elimine el ámbito cuando ya no lo necesite.</span><span class="sxs-lookup"><span data-stu-id="c2695-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="c2695-352">Instrucciones generales de IDisposable</span><span class="sxs-lookup"><span data-stu-id="c2695-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="c2695-353">No registre instancias de <xref:System.IDisposable> con una duración transitoria.</span><span class="sxs-lookup"><span data-stu-id="c2695-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="c2695-354">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="c2695-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c2695-355">No resuelva instancias de <xref:System.IDisposable> con una duración transitoria o restringida en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="c2695-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="c2695-356">La única excepción a esto es si la aplicación crea o vuelve a crear y elimina <xref:System.IServiceProvider>, pero este no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="c2695-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="c2695-357">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="c2695-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c2695-358">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="c2695-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c2695-359">Use ámbitos para controlar las duraciones de los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="c2695-360">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="c2695-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c2695-361">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="c2695-361">Default service container replacement</span></span>

<span data-ttu-id="c2695-362">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="c2695-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c2695-363">Se recomienda usar el contenedor integrado a menos que se necesite una característica específica no admitida por el contenedor, como:</span><span class="sxs-lookup"><span data-stu-id="c2695-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="c2695-364">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="c2695-364">Property injection</span></span>
* <span data-ttu-id="c2695-365">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="c2695-365">Injection based on name</span></span>
* <span data-ttu-id="c2695-366">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="c2695-366">Child containers</span></span>
* <span data-ttu-id="c2695-367">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="c2695-367">Custom lifetime management</span></span>
* <span data-ttu-id="c2695-368">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="c2695-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c2695-369">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="c2695-369">Convention-based registration</span></span>

<span data-ttu-id="c2695-370">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c2695-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c2695-371">Autofac</span><span class="sxs-lookup"><span data-stu-id="c2695-371">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c2695-372">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c2695-372">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c2695-373">Grace</span><span class="sxs-lookup"><span data-stu-id="c2695-373">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c2695-374">LightInject</span><span class="sxs-lookup"><span data-stu-id="c2695-374">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c2695-375">Lamar</span><span class="sxs-lookup"><span data-stu-id="c2695-375">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c2695-376">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c2695-376">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c2695-377">Unity</span><span class="sxs-lookup"><span data-stu-id="c2695-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="c2695-378">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="c2695-378">Thread safety</span></span>

<span data-ttu-id="c2695-379">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="c2695-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="c2695-380">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="c2695-381">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="c2695-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c2695-382">Al igual que un constructor de tipos (`static`), se garantiza que se le llame solo una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="c2695-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c2695-383">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="c2695-383">Recommendations</span></span>

* <span data-ttu-id="c2695-384">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="c2695-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="c2695-385">Como C# no admite constructores asincrónicos, use los métodos asincrónicos después de resolver sincrónicamente el servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="c2695-386">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c2695-387">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c2695-388">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c2695-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c2695-389">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="c2695-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="c2695-390">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c2695-391">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-391">Avoid static access to services.</span></span> <span data-ttu-id="c2695-392">Por ejemplo, evite capturar [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) como campo estático o propiedad para su uso en otra parte.</span><span class="sxs-lookup"><span data-stu-id="c2695-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="c2695-393">Mantenga los generadores de DI rápidos y sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="c2695-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="c2695-394">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="c2695-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="c2695-395">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService%2A> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="c2695-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="c2695-396">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="c2695-396">**Incorrect:**</span></span>

    ![Código incorrecto](dependency-injection/_static/bad.png)

  <span data-ttu-id="c2695-398">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="c2695-398">**Correct**:</span></span>

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
* <span data-ttu-id="c2695-399">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c2695-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="c2695-400">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="c2695-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="c2695-401">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c2695-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="c2695-402">Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="c2695-403">La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="c2695-404">Por ejemplo, considere el caso donde `LoginPath` se carga desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="c2695-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="c2695-405">Evite el enfoque siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2695-405">Avoid the following approach:</span></span>

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="c2695-407">En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:</span><span class="sxs-lookup"><span data-stu-id="c2695-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="c2695-408">ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="c2695-409">Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".</span><span class="sxs-lookup"><span data-stu-id="c2695-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="c2695-410">La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores.</span><span class="sxs-lookup"><span data-stu-id="c2695-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="c2695-411">Una manera correcta de obtener `LoginPath` es usar la compatibilidad integrada del patrón de opciones para DI:</span><span class="sxs-lookup"><span data-stu-id="c2695-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="c2695-412">El contenedor captura a los servicios transitorios descartables para su eliminación.</span><span class="sxs-lookup"><span data-stu-id="c2695-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="c2695-413">Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="c2695-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="c2695-414">Habilite la validación con ámbito para asegurarse de que la aplicación no tenga singletons que capturen los servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="c2695-414">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="c2695-415">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="c2695-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="c2695-416">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="c2695-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c2695-417">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="c2695-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c2695-418">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="c2695-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c2695-419">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c2695-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="c2695-420">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="c2695-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) es un marco de trabajo de la aplicación para compilar aplicaciones modulares y multiinquilino en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2695-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="c2695-422">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="c2695-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="c2695-423">Consulte los [ejemplos de Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="c2695-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c2695-424">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="c2695-424">Framework-provided services</span></span>

<span data-ttu-id="c2695-425">El método `Startup.ConfigureServices` registra los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c2695-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c2695-426">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c2695-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c2695-427">En el caso de las aplicaciones basadas en las plantillas de ASP.NET Core, el marco de trabajo registra más de 250 servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="c2695-428">En la tabla siguiente se ilustra una pequeña muestra de estos servicios registrados por el marco:</span><span class="sxs-lookup"><span data-stu-id="c2695-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="c2695-429">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="c2695-429">Service Type</span></span>                                                                                    | <span data-ttu-id="c2695-430">Período de duración</span><span class="sxs-lookup"><span data-stu-id="c2695-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c2695-431">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="c2695-432">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="c2695-433">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="c2695-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="c2695-435">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="c2695-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="c2695-437">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="c2695-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="c2695-439">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="c2695-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="c2695-441">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="c2695-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="c2695-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="c2695-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c2695-445">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c2695-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="c2695-446">Patrones de la Conferencia NDC para el desarrollo de aplicaciones de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c2695-447">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2695-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c2695-448">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c2695-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c2695-449">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="c2695-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c2695-450">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="c2695-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="c2695-451">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2695-451">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2695-452">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c2695-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c2695-453">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c2695-454">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c2695-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c2695-455">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2695-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c2695-456">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-456">Overview of dependency injection</span></span>

<span data-ttu-id="c2695-457">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="c2695-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c2695-458">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2695-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="c2695-459">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="c2695-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c2695-460">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="c2695-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="c2695-461">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c2695-462">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="c2695-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c2695-463">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="c2695-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c2695-464">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="c2695-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c2695-465">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c2695-466">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="c2695-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c2695-467">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="c2695-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c2695-468">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="c2695-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c2695-469">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c2695-470">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c2695-471">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="c2695-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c2695-472">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c2695-473">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="c2695-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c2695-474">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="c2695-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c2695-475">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c2695-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c2695-476">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="c2695-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c2695-477">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="c2695-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="c2695-478">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="c2695-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c2695-479">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c2695-480">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="c2695-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c2695-481">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="c2695-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c2695-482">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="c2695-483">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c2695-484">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="c2695-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="c2695-485">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="c2695-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="c2695-486">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c2695-487">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="c2695-488">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="c2695-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="c2695-489">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="c2695-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="c2695-490">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="c2695-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="c2695-491">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="c2695-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="c2695-492">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="c2695-493">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="c2695-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="c2695-494">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="c2695-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="c2695-495">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="c2695-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="c2695-496">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="c2695-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="c2695-497">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="c2695-497">Services injected into Startup</span></span>

<span data-ttu-id="c2695-498">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="c2695-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c2695-499">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c2695-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c2695-500">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c2695-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c2695-501">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="c2695-501">Framework-provided services</span></span>

<span data-ttu-id="c2695-502">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c2695-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c2695-503">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c2695-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c2695-504">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="c2695-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="c2695-505">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="c2695-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c2695-506">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="c2695-506">Service Type</span></span> | <span data-ttu-id="c2695-507">Período de duración</span><span class="sxs-lookup"><span data-stu-id="c2695-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c2695-508">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="c2695-509">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="c2695-510">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c2695-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c2695-512">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c2695-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c2695-514">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c2695-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c2695-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c2695-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c2695-518">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c2695-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c2695-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c2695-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c2695-522">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="c2695-522">Register additional services with extension methods</span></span>

<span data-ttu-id="c2695-523">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="c2695-524">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="c2695-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="c2695-525">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="c2695-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="c2695-526">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="c2695-526">Service lifetimes</span></span>

<span data-ttu-id="c2695-527">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="c2695-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c2695-528">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c2695-529">Transitorio</span><span class="sxs-lookup"><span data-stu-id="c2695-529">Transient</span></span>

<span data-ttu-id="c2695-530">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="c2695-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c2695-531">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="c2695-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c2695-532">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c2695-533">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="c2695-533">Scoped</span></span>

<span data-ttu-id="c2695-534">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="c2695-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c2695-535">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="c2695-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="c2695-536">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c2695-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c2695-537">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c2695-538">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="c2695-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c2695-539">Singleton</span><span class="sxs-lookup"><span data-stu-id="c2695-539">Singleton</span></span>

<span data-ttu-id="c2695-540">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="c2695-541">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="c2695-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c2695-542">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="c2695-543">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="c2695-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="c2695-544">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="c2695-545">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c2695-546">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="c2695-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c2695-547">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="c2695-547">Service registration methods</span></span>

<span data-ttu-id="c2695-548">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="c2695-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="c2695-549">Método</span><span class="sxs-lookup"><span data-stu-id="c2695-549">Method</span></span> | <span data-ttu-id="c2695-550">Automático</span><span class="sxs-lookup"><span data-stu-id="c2695-550">Automatic</span></span><br><span data-ttu-id="c2695-551">objeto</span><span class="sxs-lookup"><span data-stu-id="c2695-551">object</span></span><br><span data-ttu-id="c2695-552">eliminación</span><span class="sxs-lookup"><span data-stu-id="c2695-552">disposal</span></span> | <span data-ttu-id="c2695-553">Múltiple</span><span class="sxs-lookup"><span data-stu-id="c2695-553">Multiple</span></span><br><span data-ttu-id="c2695-554">implementaciones</span><span class="sxs-lookup"><span data-stu-id="c2695-554">implementations</span></span> | <span data-ttu-id="c2695-555">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="c2695-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c2695-556">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2695-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c2695-557">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-557">Yes</span></span> | <span data-ttu-id="c2695-558">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-558">Yes</span></span> | <span data-ttu-id="c2695-559">No</span><span class="sxs-lookup"><span data-stu-id="c2695-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-560">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="c2695-561">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-561">Yes</span></span> | <span data-ttu-id="c2695-562">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-562">Yes</span></span> | <span data-ttu-id="c2695-563">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c2695-564">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c2695-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c2695-565">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-565">Yes</span></span> | <span data-ttu-id="c2695-566">No</span><span class="sxs-lookup"><span data-stu-id="c2695-566">No</span></span> | <span data-ttu-id="c2695-567">No</span><span class="sxs-lookup"><span data-stu-id="c2695-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-568">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="c2695-569">No</span><span class="sxs-lookup"><span data-stu-id="c2695-569">No</span></span> | <span data-ttu-id="c2695-570">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-570">Yes</span></span> | <span data-ttu-id="c2695-571">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c2695-572">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="c2695-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="c2695-573">No</span><span class="sxs-lookup"><span data-stu-id="c2695-573">No</span></span> | <span data-ttu-id="c2695-574">No</span><span class="sxs-lookup"><span data-stu-id="c2695-574">No</span></span> | <span data-ttu-id="c2695-575">Sí</span><span class="sxs-lookup"><span data-stu-id="c2695-575">Yes</span></span> |

<span data-ttu-id="c2695-576">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="c2695-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c2695-577">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="c2695-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c2695-578">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="c2695-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c2695-579">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="c2695-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c2695-580">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="c2695-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c2695-581">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="c2695-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c2695-582">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="c2695-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c2695-583">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c2695-584">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="c2695-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c2695-585">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="c2695-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="c2695-586">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="c2695-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c2695-587">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="c2695-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c2695-588">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="c2695-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c2695-589">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="c2695-589">Constructor injection behavior</span></span>

<span data-ttu-id="c2695-590">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="c2695-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c2695-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="c2695-592">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="c2695-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="c2695-593">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="c2695-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c2695-594">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="c2695-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c2695-595">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="c2695-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c2695-596">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c2695-597">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c2695-597">Entity Framework contexts</span></span>

<span data-ttu-id="c2695-598">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c2695-599">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c2695-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c2695-600">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c2695-601">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="c2695-601">Lifetime and registration options</span></span>

<span data-ttu-id="c2695-602">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="c2695-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="c2695-603">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="c2695-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c2695-604">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c2695-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c2695-605">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="c2695-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="c2695-606">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="c2695-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="c2695-607">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="c2695-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="c2695-608">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c2695-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="c2695-609">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="c2695-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="c2695-610">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="c2695-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="c2695-611">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="c2695-612">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="c2695-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="c2695-613">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="c2695-614">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="c2695-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="c2695-615">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="c2695-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="c2695-616">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="c2695-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="c2695-617">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="c2695-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="c2695-618">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="c2695-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="c2695-619">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="c2695-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="c2695-620">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="c2695-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="c2695-621">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="c2695-621">**First request:**</span></span>

<span data-ttu-id="c2695-622">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="c2695-622">Controller operations:</span></span>

<span data-ttu-id="c2695-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="c2695-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="c2695-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c2695-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c2695-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c2695-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c2695-626">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c2695-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c2695-627">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c2695-627">`OperationService` operations:</span></span>

<span data-ttu-id="c2695-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="c2695-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="c2695-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c2695-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c2695-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c2695-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c2695-631">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c2695-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c2695-632">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="c2695-632">**Second request:**</span></span>

<span data-ttu-id="c2695-633">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="c2695-633">Controller operations:</span></span>

<span data-ttu-id="c2695-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="c2695-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="c2695-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c2695-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c2695-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c2695-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c2695-637">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c2695-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c2695-638">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="c2695-638">`OperationService` operations:</span></span>

<span data-ttu-id="c2695-639">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="c2695-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="c2695-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c2695-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c2695-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c2695-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c2695-642">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c2695-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c2695-643">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="c2695-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="c2695-644">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="c2695-644">*Transient* objects are always different.</span></span> <span data-ttu-id="c2695-645">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="c2695-646">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="c2695-647">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="c2695-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="c2695-648">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="c2695-649">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="c2695-649">Call services from main</span></span>

<span data-ttu-id="c2695-650">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c2695-651">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="c2695-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="c2695-652">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c2695-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="c2695-653">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="c2695-653">Scope validation</span></span>

<span data-ttu-id="c2695-654">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2695-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c2695-655">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="c2695-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c2695-656">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="c2695-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c2695-657">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="c2695-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c2695-658">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="c2695-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c2695-659">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="c2695-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c2695-660">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="c2695-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c2695-661">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="c2695-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c2695-662">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="c2695-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="c2695-663">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="c2695-663">Request Services</span></span>

<span data-ttu-id="c2695-664">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="c2695-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c2695-665">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c2695-666">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c2695-667">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="c2695-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c2695-668">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="c2695-669">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="c2695-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="c2695-670">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="c2695-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c2695-671">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="c2695-671">Design services for dependency injection</span></span>

<span data-ttu-id="c2695-672">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="c2695-672">Best practices are to:</span></span>

* <span data-ttu-id="c2695-673">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c2695-674">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="c2695-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c2695-675">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="c2695-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c2695-676">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c2695-677">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="c2695-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c2695-678">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="c2695-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c2695-679">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="c2695-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c2695-680">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="c2695-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c2695-681">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="c2695-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="c2695-682">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="c2695-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c2695-683">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="c2695-683">Disposal of services</span></span>

<span data-ttu-id="c2695-684">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="c2695-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c2695-685">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="c2695-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="c2695-686">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="c2695-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="c2695-687">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c2695-687">In the following example:</span></span>

* <span data-ttu-id="c2695-688">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c2695-689">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="c2695-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c2695-690">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="c2695-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c2695-691">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c2695-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c2695-692">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="c2695-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c2695-693">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="c2695-693">Transient, limited lifetime</span></span>

<span data-ttu-id="c2695-694">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="c2695-694">**Scenario**</span></span>

<span data-ttu-id="c2695-695">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="c2695-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c2695-696">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="c2695-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="c2695-697">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="c2695-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c2695-698">**Solución**</span><span class="sxs-lookup"><span data-stu-id="c2695-698">**Solution**</span></span>

<span data-ttu-id="c2695-699">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="c2695-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c2695-700">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="c2695-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c2695-701">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="c2695-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c2695-702">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="c2695-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c2695-703">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c2695-704">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="c2695-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c2695-705">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="c2695-705">**Scenario**</span></span>

<span data-ttu-id="c2695-706">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="c2695-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c2695-707">**Solución**</span><span class="sxs-lookup"><span data-stu-id="c2695-707">**Solution**</span></span>

<span data-ttu-id="c2695-708">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="c2695-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c2695-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="c2695-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c2695-710">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="c2695-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c2695-711">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="c2695-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="c2695-712">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="c2695-712">General Guidelines</span></span>

* <span data-ttu-id="c2695-713">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="c2695-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c2695-714">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="c2695-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c2695-715">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="c2695-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c2695-716">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="c2695-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c2695-717">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="c2695-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c2695-718">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="c2695-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c2695-719">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c2695-720">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="c2695-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c2695-721">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="c2695-721">Default service container replacement</span></span>

<span data-ttu-id="c2695-722">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="c2695-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c2695-723">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="c2695-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c2695-724">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="c2695-724">Property injection</span></span>
* <span data-ttu-id="c2695-725">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="c2695-725">Injection based on name</span></span>
* <span data-ttu-id="c2695-726">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="c2695-726">Child containers</span></span>
* <span data-ttu-id="c2695-727">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="c2695-727">Custom lifetime management</span></span>
* <span data-ttu-id="c2695-728">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="c2695-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c2695-729">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="c2695-729">Convention-based registration</span></span>

<span data-ttu-id="c2695-730">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c2695-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c2695-731">Autofac</span><span class="sxs-lookup"><span data-stu-id="c2695-731">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c2695-732">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c2695-732">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c2695-733">Grace</span><span class="sxs-lookup"><span data-stu-id="c2695-733">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c2695-734">LightInject</span><span class="sxs-lookup"><span data-stu-id="c2695-734">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c2695-735">Lamar</span><span class="sxs-lookup"><span data-stu-id="c2695-735">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c2695-736">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c2695-736">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c2695-737">Unity</span><span class="sxs-lookup"><span data-stu-id="c2695-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c2695-738">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="c2695-738">Thread safety</span></span>

<span data-ttu-id="c2695-739">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="c2695-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="c2695-740">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="c2695-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c2695-741">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="c2695-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c2695-742">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="c2695-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c2695-743">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="c2695-743">Recommendations</span></span>

* <span data-ttu-id="c2695-744">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="c2695-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c2695-745">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="c2695-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="c2695-746">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c2695-747">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c2695-748">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c2695-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c2695-749">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="c2695-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c2695-750">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="c2695-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c2695-751">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="c2695-751">Avoid static access to services.</span></span> <span data-ttu-id="c2695-752">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="c2695-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="c2695-753">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="c2695-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="c2695-754">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="c2695-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="c2695-755">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="c2695-755">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="c2695-756">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="c2695-756">**Correct**:</span></span>

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

* <span data-ttu-id="c2695-757">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="c2695-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="c2695-758">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="c2695-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="c2695-759">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="c2695-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c2695-760">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="c2695-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c2695-761">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="c2695-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c2695-762">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c2695-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2695-763">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c2695-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c2695-764">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2695-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c2695-765">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c2695-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c2695-766">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="c2695-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="c2695-767">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="c2695-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="c2695-768">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2695-768">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
