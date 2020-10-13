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
ms.openlocfilehash: 99e0109ea4c2526e9f91a8a4df23c4557e9be83a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762313"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="786b2-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="786b2-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="786b2-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="786b2-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="786b2-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="786b2-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="786b2-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="786b2-107">Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="786b2-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="786b2-108">En este tema se proporciona información sobre la inyección de dependencias en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="786b2-108">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="786b2-109">Para obtener información sobre el uso de la inyección de dependencias en las aplicaciones de consola, consulte [Inyección de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="786b2-109">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="786b2-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="786b2-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="786b2-111">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-111">Overview of dependency injection</span></span>

<span data-ttu-id="786b2-112">Una *dependencia* es un objeto del que depende otro objeto.</span><span class="sxs-lookup"><span data-stu-id="786b2-112">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="786b2-113">Examine la clase `MyDependency` siguiente con un método `WriteMessage` del que dependen otras clases:</span><span class="sxs-lookup"><span data-stu-id="786b2-113">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="786b2-114">Una clase puede crear una instancia de la clase `MyDependency` para usar su método `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="786b2-114">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="786b2-115">En el ejemplo siguiente, la clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="786b2-115">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="786b2-116">La clase crea y depende directamente de la clase `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-116">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="786b2-117">Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las razones siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-117">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="786b2-118">Para reemplazar `MyDependency` por una implementación diferente, se debe modificar la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="786b2-118">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="786b2-119">Si `MyDependency` tiene dependencias, deben configurarse según la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="786b2-119">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="786b2-120">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-120">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="786b2-121">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="786b2-121">This implementation is difficult to unit test.</span></span> <span data-ttu-id="786b2-122">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="786b2-122">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="786b2-123">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="786b2-123">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="786b2-124">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-124">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="786b2-125">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-125">Registration of the dependency in a service container.</span></span> <span data-ttu-id="786b2-126">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="786b2-126">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="786b2-127">Por lo general, los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-127">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="786b2-128">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="786b2-128">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="786b2-129">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="786b2-129">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="786b2-130">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define el método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="786b2-130">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="786b2-131">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="786b2-131">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="786b2-132">La aplicación de ejemplo registra el servicio `IMyDependency` con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-132">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="786b2-133">El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-133">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="786b2-134">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="786b2-134">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="786b2-135">En la aplicación de ejemplo, el servicio `IMyDependency` se solicita y usa para llamar al método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="786b2-135">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="786b2-136">Mediante el uso del patrón de DI, el controlador:</span><span class="sxs-lookup"><span data-stu-id="786b2-136">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="786b2-137">No usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency` que implementa.</span><span class="sxs-lookup"><span data-stu-id="786b2-137">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="786b2-138">Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.</span><span class="sxs-lookup"><span data-stu-id="786b2-138">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="786b2-139">No crea una instancia de `MyDependency`, la crea el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="786b2-139">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="786b2-140">La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:</span><span class="sxs-lookup"><span data-stu-id="786b2-140">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="786b2-141">El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="786b2-141">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="786b2-142">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601>, el que solicita en el constructor.</span><span class="sxs-lookup"><span data-stu-id="786b2-142">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="786b2-143">`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="786b2-143">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="786b2-144">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="786b2-144">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="786b2-145">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-145">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="786b2-146">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="786b2-146">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="786b2-147">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="786b2-147">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="786b2-148">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="786b2-148">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="786b2-149">En la terminología de la inserción de dependencias, un servicio:</span><span class="sxs-lookup"><span data-stu-id="786b2-149">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="786b2-150">Por lo general, es un objeto que proporciona un servicio a otros objetos, como el servicio `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-150">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="786b2-151">No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.</span><span class="sxs-lookup"><span data-stu-id="786b2-151">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="786b2-152">El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido.</span><span class="sxs-lookup"><span data-stu-id="786b2-152">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="786b2-153">Las implementaciones de `IMyDependency` que se muestran en los ejemplos anteriores se escribieron para mostrar la inserción de DI básica, no para implementar el registro.</span><span class="sxs-lookup"><span data-stu-id="786b2-153">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="786b2-154">La mayoría de las aplicaciones no deberían tener que escribir registradores.</span><span class="sxs-lookup"><span data-stu-id="786b2-154">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="786b2-155">En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="786b2-155">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="786b2-156">Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="786b2-156">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="786b2-157">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="786b2-157">Services injected into Startup</span></span>

<span data-ttu-id="786b2-158">Los servicios se pueden insertar en el constructor `Startup` y en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="786b2-158">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="786b2-159">Solo se pueden insertar los servicios siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="786b2-159">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="786b2-160">Cualquier servicio registrado con el contenedor de DI se puede insertar en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="786b2-160">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="786b2-161">Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="786b2-161">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="786b2-162">Registro de grupos de servicios con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="786b2-162">Register groups of services with extension methods</span></span>

<span data-ttu-id="786b2-163">El marco ASP.NET Core usa una convención para registrar un grupo de servicios relacionados.</span><span class="sxs-lookup"><span data-stu-id="786b2-163">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="786b2-164">La convención es usar un único método de extensión de `Add{GROUP_NAME}` para registrar todos los servicios requeridos por una característica de marco.</span><span class="sxs-lookup"><span data-stu-id="786b2-164">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="786b2-165">Por ejemplo, el método de extensión <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> registra los servicios necesarios para los controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="786b2-165">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="786b2-166">El código siguiente lo genera la plantilla de Razor Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="786b2-166">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="786b2-167">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="786b2-167">Service lifetimes</span></span>

<span data-ttu-id="786b2-168">Los servicios se pueden registrar con una de las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-168">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="786b2-169">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-169">Transient</span></span>
* <span data-ttu-id="786b2-170">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="786b2-170">Scoped</span></span>
* <span data-ttu-id="786b2-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-171">Singleton</span></span>

<span data-ttu-id="786b2-172">En las secciones siguientes se describen cada una de las duraciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="786b2-172">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="786b2-173">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="786b2-173">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="786b2-174">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-174">Transient</span></span>

<span data-ttu-id="786b2-175">Los servicios de duración transitoria se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="786b2-175">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="786b2-176">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="786b2-176">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="786b2-177">Registre los servicios transitorios con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span><span class="sxs-lookup"><span data-stu-id="786b2-177">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="786b2-178">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-178">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="786b2-179">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="786b2-179">Scoped</span></span>

<span data-ttu-id="786b2-180">Los servicios de duración con ámbito se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="786b2-180">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="786b2-181">Registre los servicios con ámbito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span><span class="sxs-lookup"><span data-stu-id="786b2-181">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="786b2-182">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-182">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="786b2-183">Cuando se usa Entity Framework Core, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> registra tipos de `DbContext` con una duración de ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="786b2-183">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="786b2-184">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-184">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="786b2-185">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="786b2-185">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="786b2-186">Basta con:</span><span class="sxs-lookup"><span data-stu-id="786b2-186">It's fine to:</span></span>

* <span data-ttu-id="786b2-187">Resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="786b2-187">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="786b2-188">Resolver un servicio con ámbito desde otro servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="786b2-188">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="786b2-189">De manera predeterminada, en el entorno de desarrollo, resolver un servicio desde otro servicio con una duración más larga genera una excepción.</span><span class="sxs-lookup"><span data-stu-id="786b2-189">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="786b2-190">Para más información, vea [Validación del ámbito](#sv).</span><span class="sxs-lookup"><span data-stu-id="786b2-190">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="786b2-191">Para usar servicios con ámbito en middleware, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-191">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="786b2-192">Inserte el servicio en el método `Invoke` o `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="786b2-192">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="786b2-193">El uso de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) genera una excepción en tiempo de ejecución porque obliga al servicio con ámbito a comportarse como un singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-193">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="786b2-194">El ejemplo que aparece en la sección [Opciones de registro y duración](#lifetime-and-registration-options) muestra el enfoque `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="786b2-194">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="786b2-195">Use [middleware basado en Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="786b2-195">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="786b2-196">El middleware registrado mediante este enfoque se activa por solicitud de cliente (conexión), lo que permite que los servicios con ámbito se inserten en el método `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="786b2-196">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="786b2-197">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="786b2-197">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="786b2-198">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-198">Singleton</span></span>

<span data-ttu-id="786b2-199">Los servicios de duración de singleton se crean de alguna de las formas siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-199">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="786b2-200">La primera vez que se solicitan.</span><span class="sxs-lookup"><span data-stu-id="786b2-200">The first time they're requested.</span></span>
* <span data-ttu-id="786b2-201">Mediante el desarrollador, al proporcionar una instancia de implementación directamente al contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-201">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="786b2-202">Este enfoque rara vez es necesario.</span><span class="sxs-lookup"><span data-stu-id="786b2-202">This approach is rarely needed.</span></span>

<span data-ttu-id="786b2-203">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="786b2-203">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="786b2-204">Si la aplicación requiere un comportamiento de singleton, permita que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-204">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="786b2-205">No implemente el modelo de diseño singleton y proporcione el código para desechar el singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-205">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="786b2-206">Los servicios nunca deben desecharse mediante el código que haya resuelto el servicio del contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-206">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="786b2-207">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="786b2-207">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="786b2-208">Registre los servicios singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="786b2-208">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="786b2-209">Los servicios singleton deben ser seguros para los subprocesos y se suelen usar en servicios sin estado.</span><span class="sxs-lookup"><span data-stu-id="786b2-209">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="786b2-210">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-210">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="786b2-211">Como no se libera memoria hasta que se apaga la aplicación, se debe tener en cuenta el uso de memoria con un servicio singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-211">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="786b2-212">***Nunca*** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-212">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="786b2-213">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="786b2-213">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="786b2-214">Basta con resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="786b2-214">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="786b2-215">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="786b2-215">Service registration methods</span></span>

<span data-ttu-id="786b2-216">El marco proporciona métodos de extensión de registro del servicio que resultan útiles en escenarios específicos:</span><span class="sxs-lookup"><span data-stu-id="786b2-216">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="786b2-217">Método</span><span class="sxs-lookup"><span data-stu-id="786b2-217">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="786b2-218">Automático</span><span class="sxs-lookup"><span data-stu-id="786b2-218">Automatic</span></span><br><span data-ttu-id="786b2-219">objeto</span><span class="sxs-lookup"><span data-stu-id="786b2-219">object</span></span><br><span data-ttu-id="786b2-220">eliminación</span><span class="sxs-lookup"><span data-stu-id="786b2-220">disposal</span></span> | <span data-ttu-id="786b2-221">Múltiple</span><span class="sxs-lookup"><span data-stu-id="786b2-221">Multiple</span></span><br><span data-ttu-id="786b2-222">implementaciones</span><span class="sxs-lookup"><span data-stu-id="786b2-222">implementations</span></span> | <span data-ttu-id="786b2-223">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="786b2-223">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="786b2-224">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="786b2-224">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="786b2-225">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-225">Yes</span></span>                             | <span data-ttu-id="786b2-226">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-226">Yes</span></span>                         | <span data-ttu-id="786b2-227">No</span><span class="sxs-lookup"><span data-stu-id="786b2-227">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-228">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-228">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="786b2-229">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-229">Yes</span></span>                             | <span data-ttu-id="786b2-230">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-230">Yes</span></span>                         | <span data-ttu-id="786b2-231">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-231">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="786b2-232">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="786b2-232">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="786b2-233">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-233">Yes</span></span>                             | <span data-ttu-id="786b2-234">No</span><span class="sxs-lookup"><span data-stu-id="786b2-234">No</span></span>                          | <span data-ttu-id="786b2-235">No</span><span class="sxs-lookup"><span data-stu-id="786b2-235">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-236">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-236">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="786b2-237">No</span><span class="sxs-lookup"><span data-stu-id="786b2-237">No</span></span>                              | <span data-ttu-id="786b2-238">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-238">Yes</span></span>                         | <span data-ttu-id="786b2-239">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-239">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-240">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-240">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="786b2-241">No</span><span class="sxs-lookup"><span data-stu-id="786b2-241">No</span></span>                              | <span data-ttu-id="786b2-242">No</span><span class="sxs-lookup"><span data-stu-id="786b2-242">No</span></span>                          | <span data-ttu-id="786b2-243">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-243">Yes</span></span>       |

<span data-ttu-id="786b2-244">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="786b2-244">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="786b2-245">Es habitual usar varias implementaciones al [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="786b2-245">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="786b2-246">El marco también proporciona métodos de extensión `TryAdd{LIFETIME}`, que registran el servicio solo si todavía no hay registrada una implementación.</span><span class="sxs-lookup"><span data-stu-id="786b2-246">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="786b2-247">En el ejemplo siguiente, la llamada a `AddSingleton` registra `MyDependency` como una implementación para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-247">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="786b2-248">La llamada a `TryAddSingleton` no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="786b2-248">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="786b2-249">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="786b2-249">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="786b2-250">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registran el servicio solo si todavía no hay una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="786b2-250">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="786b2-251">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-251">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="786b2-252">Al registrar los servicios, el desarrollador debería agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="786b2-252">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="786b2-253">Por lo general, los autores de bibliotecas usan `TryAddEnumerable` para evitar el registro de varias copias de una implementación en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-253">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="786b2-254">En el ejemplo siguiente, la primera llamada a `TryAddEnumerable` registra `MyDependency` como una implementación para `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="786b2-254">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="786b2-255">La segunda llamada registra `MyDependency` para `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="786b2-255">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="786b2-256">La tercera llamada no tiene ningún efecto porque `IMyDependency1` ya tiene una implementación registrada de `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="786b2-256">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="786b2-257">Normalmente, el registro del servicio es independiente, excepto cuando se registran varias implementaciones del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="786b2-257">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="786b2-258">`IServiceCollection` es una colección de objetos <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="786b2-258">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="786b2-259">En el ejemplo siguiente se muestra cómo registrar un servicio mediante la creación e incorporación de un elemento `ServiceDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="786b2-259">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="786b2-260">Los métodos `Add{LIFETIME}` integrados usan el mismo enfoque.</span><span class="sxs-lookup"><span data-stu-id="786b2-260">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="786b2-261">Por ejemplo, consulte el [código fuente de AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="786b2-261">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="786b2-262">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="786b2-262">Constructor injection behavior</span></span>

<span data-ttu-id="786b2-263">Los servicios se pueden resolver mediante el uso de:</span><span class="sxs-lookup"><span data-stu-id="786b2-263">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="786b2-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="786b2-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="786b2-265">Crea objetos que no están registrados en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-265">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="786b2-266">Se utiliza con características de marcos, como [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), controladores MVC y [enlazadores de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="786b2-266">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="786b2-267">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="786b2-267">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="786b2-268">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="786b2-268">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="786b2-269">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="786b2-269">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="786b2-270">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-270">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="786b2-271">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="786b2-271">Entity Framework contexts</span></span>

<span data-ttu-id="786b2-272">De manera predeterminada, los contextos de Entity Framework se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-272">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="786b2-273">Para usar una duración distinta, especifíquela mediante el uso de una sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="786b2-273">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="786b2-274">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que la del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-274">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="786b2-275">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="786b2-275">Lifetime and registration options</span></span>

<span data-ttu-id="786b2-276">Para mostrar la diferencia entre las duraciones del servicio y sus opciones de registro, considere las interfaces siguientes que representan una tarea como una operación con un identificador, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="786b2-276">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="786b2-277">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona las mismas instancias del servicio u otras distintas cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="786b2-277">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="786b2-278">La clase `Operation` siguiente implementa todas las interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="786b2-278">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="786b2-279">El constructor `Operation` genera un GUID y almacena los 4 últimos caracteres en la propiedad `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="786b2-279">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="786b2-280">El método `Startup.ConfigureServices` crea varios registros de la clase `Operation` de acuerdo con las duraciones mencionadas:</span><span class="sxs-lookup"><span data-stu-id="786b2-280">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="786b2-281">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="786b2-281">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="786b2-282">`IndexModel` y el middleware solicitan cada clase del tipo `IOperation` y registran el valor de `OperationId` de cada una:</span><span class="sxs-lookup"><span data-stu-id="786b2-282">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="786b2-283">De manera similar al `IndexModel`, el middleware resuelve los mismos servicios:</span><span class="sxs-lookup"><span data-stu-id="786b2-283">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="786b2-284">Los servicios con ámbito se deben resolver en el método `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="786b2-284">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="786b2-285">La salida del registrador muestra:</span><span class="sxs-lookup"><span data-stu-id="786b2-285">The logger output shows:</span></span>

* <span data-ttu-id="786b2-286">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="786b2-286">*Transient* objects are always different.</span></span> <span data-ttu-id="786b2-287">El valor `OperationId` transitorio es distinto en el `IndexModel` y en el middleware.</span><span class="sxs-lookup"><span data-stu-id="786b2-287">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="786b2-288">Los objetos *con ámbito* son iguales para cada solicitud, pero varían entre solicitudes.</span><span class="sxs-lookup"><span data-stu-id="786b2-288">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="786b2-289">Los objetos *singleton* son los mismos para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-289">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="786b2-290">Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="786b2-290">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="786b2-291">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="786b2-291">Call services from main</span></span>

<span data-ttu-id="786b2-292">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="786b2-293">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="786b2-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="786b2-294">En el ejemplo siguiente se muestra cómo acceder al servicio `IMyDependency` con ámbito y llamar a su método `WriteMessage` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="786b2-294">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="786b2-295">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="786b2-295">Scope validation</span></span>

<span data-ttu-id="786b2-296">Cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments) y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="786b2-296">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="786b2-297">Los servicios con ámbito no se resuelven desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="786b2-297">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="786b2-298">Los servicios con ámbito no se insertan en singletons.</span><span class="sxs-lookup"><span data-stu-id="786b2-298">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="786b2-299">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="786b2-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="786b2-300">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="786b2-300">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="786b2-301">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="786b2-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="786b2-302">Si un servicio con ámbito se crea en el contenedor raíz, su duración sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="786b2-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="786b2-303">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="786b2-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="786b2-304">Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="786b2-304">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="786b2-305">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="786b2-305">Request Services</span></span>

<span data-ttu-id="786b2-306">Los servicios disponibles en una solicitud de ASP.NET Core se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="786b2-306">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="786b2-307">Cuando se solicitan servicios dentro de una solicitud, los servicios y sus dependencias se resuelven desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-307">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="786b2-308">El marco crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="786b2-308">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="786b2-309">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="786b2-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="786b2-310">Se recomienda que solicite las dependencias como parámetros del constructor para resolver los servicios desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-310">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="786b2-311">Esto genera clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="786b2-311">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="786b2-312">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-312">Design services for dependency injection</span></span>

<span data-ttu-id="786b2-313">Al diseñar servicios para la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="786b2-313">When designing services for dependency injection:</span></span>

* <span data-ttu-id="786b2-314">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="786b2-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="786b2-315">Evitar crear un estado global mediante el diseño de aplicaciones para usar servicios singleton en su lugar.</span><span class="sxs-lookup"><span data-stu-id="786b2-315">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="786b2-316">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="786b2-317">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="786b2-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="786b2-318">Cree servicios pequeños, bien factorizados y probados con facilidad.</span><span class="sxs-lookup"><span data-stu-id="786b2-318">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="786b2-319">Si una clase tiene muchas dependencias insertadas, podría ser un signo de que la clase tiene demasiadas responsabilidades e infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="786b2-319">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="786b2-320">Trate de mover algunas de las responsabilidades de la clase a clases nuevas para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="786b2-320">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="786b2-321">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="786b2-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="786b2-322">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="786b2-322">Disposal of services</span></span>

<span data-ttu-id="786b2-323">El contenedor llama a <xref:System.IDisposable.Dispose%2A> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="786b2-323">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="786b2-324">El desarrollador nunca debe eliminar los servicios resueltos desde el contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-324">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="786b2-325">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="786b2-325">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="786b2-326">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="786b2-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="786b2-327">La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:</span><span class="sxs-lookup"><span data-stu-id="786b2-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="786b2-328">Servicios no creados por el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="786b2-328">Services not created by the service container</span></span>

<span data-ttu-id="786b2-329">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="786b2-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="786b2-330">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="786b2-330">In the preceding code:</span></span>

* <span data-ttu-id="786b2-331">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="786b2-332">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="786b2-332">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="786b2-333">El desarrollador es responsable de la eliminación de los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-333">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="786b2-334">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="786b2-334">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="786b2-335">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="786b2-335">Transient, limited lifetime</span></span>

<span data-ttu-id="786b2-336">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="786b2-336">**Scenario**</span></span>

<span data-ttu-id="786b2-337">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="786b2-337">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="786b2-338">La instancia se resuelve en el ámbito raíz (contenedor raíz).</span><span class="sxs-lookup"><span data-stu-id="786b2-338">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="786b2-339">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="786b2-339">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="786b2-340">**Solución**</span><span class="sxs-lookup"><span data-stu-id="786b2-340">**Solution**</span></span>

<span data-ttu-id="786b2-341">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="786b2-341">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="786b2-342">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="786b2-342">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="786b2-343">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="786b2-343">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="786b2-344">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="786b2-344">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="786b2-345">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-345">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="786b2-346">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="786b2-346">Shared instance, limited lifetime</span></span>

<span data-ttu-id="786b2-347">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="786b2-347">**Scenario**</span></span>

<span data-ttu-id="786b2-348">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero la instancia de <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="786b2-348">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="786b2-349">**Solución**</span><span class="sxs-lookup"><span data-stu-id="786b2-349">**Solution**</span></span>

<span data-ttu-id="786b2-350">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="786b2-350">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="786b2-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="786b2-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="786b2-352">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="786b2-352">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="786b2-353">Elimine el ámbito cuando ya no lo necesite.</span><span class="sxs-lookup"><span data-stu-id="786b2-353">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="786b2-354">Instrucciones generales de IDisposable</span><span class="sxs-lookup"><span data-stu-id="786b2-354">General IDisposable guidelines</span></span>

* <span data-ttu-id="786b2-355">No registre instancias de <xref:System.IDisposable> con una duración transitoria.</span><span class="sxs-lookup"><span data-stu-id="786b2-355">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="786b2-356">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="786b2-356">Use the factory pattern instead.</span></span>
* <span data-ttu-id="786b2-357">No resuelva instancias de <xref:System.IDisposable> con una duración transitoria o restringida en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="786b2-357">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="786b2-358">La única excepción a esto es si la aplicación crea o vuelve a crear y elimina <xref:System.IServiceProvider>, pero este no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="786b2-358">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="786b2-359">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="786b2-359">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="786b2-360">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="786b2-360">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="786b2-361">Use ámbitos para controlar las duraciones de los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-361">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="786b2-362">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="786b2-362">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="786b2-363">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="786b2-363">Default service container replacement</span></span>

<span data-ttu-id="786b2-364">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="786b2-364">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="786b2-365">Se recomienda usar el contenedor integrado a menos que se necesite una característica específica no admitida por el contenedor, como:</span><span class="sxs-lookup"><span data-stu-id="786b2-365">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="786b2-366">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="786b2-366">Property injection</span></span>
* <span data-ttu-id="786b2-367">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="786b2-367">Injection based on name</span></span>
* <span data-ttu-id="786b2-368">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="786b2-368">Child containers</span></span>
* <span data-ttu-id="786b2-369">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="786b2-369">Custom lifetime management</span></span>
* <span data-ttu-id="786b2-370">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="786b2-370">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="786b2-371">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="786b2-371">Convention-based registration</span></span>

<span data-ttu-id="786b2-372">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="786b2-372">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="786b2-373">Autofac</span><span class="sxs-lookup"><span data-stu-id="786b2-373">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="786b2-374">DryIoc</span><span class="sxs-lookup"><span data-stu-id="786b2-374">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="786b2-375">Grace</span><span class="sxs-lookup"><span data-stu-id="786b2-375">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="786b2-376">LightInject</span><span class="sxs-lookup"><span data-stu-id="786b2-376">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="786b2-377">Lamar</span><span class="sxs-lookup"><span data-stu-id="786b2-377">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="786b2-378">Stashbox</span><span class="sxs-lookup"><span data-stu-id="786b2-378">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="786b2-379">Unity</span><span class="sxs-lookup"><span data-stu-id="786b2-379">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="786b2-380">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="786b2-380">Thread safety</span></span>

<span data-ttu-id="786b2-381">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="786b2-381">Create thread-safe singleton services.</span></span> <span data-ttu-id="786b2-382">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-382">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="786b2-383">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="786b2-383">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="786b2-384">Al igual que un constructor de tipos (`static`), se garantiza que se le llame solo una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="786b2-384">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="786b2-385">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="786b2-385">Recommendations</span></span>

* <span data-ttu-id="786b2-386">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="786b2-386">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="786b2-387">Como C# no admite constructores asincrónicos, use los métodos asincrónicos después de resolver sincrónicamente el servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-387">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="786b2-388">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-388">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="786b2-389">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-389">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="786b2-390">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="786b2-390">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="786b2-391">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="786b2-391">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="786b2-392">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-392">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="786b2-393">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-393">Avoid static access to services.</span></span> <span data-ttu-id="786b2-394">Por ejemplo, evite capturar [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) como campo estático o propiedad para su uso en otra parte.</span><span class="sxs-lookup"><span data-stu-id="786b2-394">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="786b2-395">Mantenga los generadores de DI rápidos y sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="786b2-395">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="786b2-396">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="786b2-396">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="786b2-397">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService%2A> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="786b2-397">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="786b2-398">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="786b2-398">**Incorrect:**</span></span>

    ![Código incorrecto](dependency-injection/_static/bad.png)

  <span data-ttu-id="786b2-400">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="786b2-400">**Correct**:</span></span>

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
* <span data-ttu-id="786b2-401">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="786b2-401">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="786b2-402">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="786b2-402">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="786b2-403">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="786b2-403">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="786b2-404">Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-404">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="786b2-405">La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-405">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="786b2-406">Por ejemplo, considere el caso donde `LoginPath` se carga desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="786b2-406">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="786b2-407">Evite el enfoque siguiente:</span><span class="sxs-lookup"><span data-stu-id="786b2-407">Avoid the following approach:</span></span>

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="786b2-409">En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:</span><span class="sxs-lookup"><span data-stu-id="786b2-409">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="786b2-410">ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-410">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="786b2-411">Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".</span><span class="sxs-lookup"><span data-stu-id="786b2-411">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="786b2-412">La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores.</span><span class="sxs-lookup"><span data-stu-id="786b2-412">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="786b2-413">Una manera correcta de obtener `LoginPath` es usar la compatibilidad integrada del patrón de opciones para DI:</span><span class="sxs-lookup"><span data-stu-id="786b2-413">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="786b2-414">El contenedor captura a los servicios transitorios descartables para su eliminación.</span><span class="sxs-lookup"><span data-stu-id="786b2-414">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="786b2-415">Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="786b2-415">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="786b2-416">Habilite la validación con ámbito para asegurarse de que la aplicación no tenga singletons que capturen los servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="786b2-416">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="786b2-417">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="786b2-417">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="786b2-418">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="786b2-418">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="786b2-419">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="786b2-419">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="786b2-420">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="786b2-420">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="786b2-421">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="786b2-421">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="786b2-422">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-422">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="786b2-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) es un marco de trabajo de la aplicación para compilar aplicaciones modulares y multiinquilino en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="786b2-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="786b2-424">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="786b2-424">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="786b2-425">Consulte los [ejemplos de Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="786b2-425">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="786b2-426">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="786b2-426">Framework-provided services</span></span>

<span data-ttu-id="786b2-427">El método `Startup.ConfigureServices` registra los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="786b2-427">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="786b2-428">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="786b2-428">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="786b2-429">En el caso de las aplicaciones basadas en las plantillas de ASP.NET Core, el marco de trabajo registra más de 250 servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-429">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="786b2-430">En la tabla siguiente se ilustra una pequeña muestra de estos servicios registrados por el marco:</span><span class="sxs-lookup"><span data-stu-id="786b2-430">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="786b2-431">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="786b2-431">Service Type</span></span>                                                                                    | <span data-ttu-id="786b2-432">Período de duración</span><span class="sxs-lookup"><span data-stu-id="786b2-432">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="786b2-433">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-433">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="786b2-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="786b2-435">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="786b2-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="786b2-437">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-437">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="786b2-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="786b2-439">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-439">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="786b2-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="786b2-441">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="786b2-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="786b2-443">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-443">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="786b2-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="786b2-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-445">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="786b2-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-446">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="786b2-447">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="786b2-447">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="786b2-448">Patrones de la Conferencia NDC para el desarrollo de aplicaciones de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-448">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="786b2-449">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="786b2-449">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="786b2-450">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="786b2-450">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="786b2-451">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="786b2-451">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="786b2-452">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="786b2-452">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="786b2-453">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="786b2-453">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="786b2-454">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="786b2-454">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="786b2-455">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-455">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="786b2-456">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="786b2-456">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="786b2-457">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="786b2-457">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="786b2-458">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-458">Overview of dependency injection</span></span>

<span data-ttu-id="786b2-459">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="786b2-459">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="786b2-460">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="786b2-460">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="786b2-461">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="786b2-461">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="786b2-462">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="786b2-462">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="786b2-463">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-463">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="786b2-464">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="786b2-464">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="786b2-465">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="786b2-465">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="786b2-466">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="786b2-466">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="786b2-467">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-467">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="786b2-468">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="786b2-468">This implementation is difficult to unit test.</span></span> <span data-ttu-id="786b2-469">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="786b2-469">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="786b2-470">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="786b2-470">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="786b2-471">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-471">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="786b2-472">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-472">Registration of the dependency in a service container.</span></span> <span data-ttu-id="786b2-473">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="786b2-473">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="786b2-474">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-474">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="786b2-475">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="786b2-475">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="786b2-476">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="786b2-476">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="786b2-477">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="786b2-477">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="786b2-478">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="786b2-478">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="786b2-479">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="786b2-479">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="786b2-480">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="786b2-480">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="786b2-481">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-481">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="786b2-482">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="786b2-482">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="786b2-483">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="786b2-483">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="786b2-484">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-484">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="786b2-485">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-485">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="786b2-486">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="786b2-486">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="786b2-487">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="786b2-487">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="786b2-488">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-488">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="786b2-489">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-489">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="786b2-490">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="786b2-490">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="786b2-491">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="786b2-491">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="786b2-492">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="786b2-492">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="786b2-493">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="786b2-493">We recommended that apps follow this convention.</span></span> <span data-ttu-id="786b2-494">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-494">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="786b2-495">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="786b2-495">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="786b2-496">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="786b2-496">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="786b2-497">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="786b2-497">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="786b2-498">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="786b2-498">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="786b2-499">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="786b2-499">Services injected into Startup</span></span>

<span data-ttu-id="786b2-500">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="786b2-500">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="786b2-501">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="786b2-501">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="786b2-502">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="786b2-502">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="786b2-503">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="786b2-503">Framework-provided services</span></span>

<span data-ttu-id="786b2-504">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="786b2-504">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="786b2-505">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="786b2-505">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="786b2-506">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="786b2-506">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="786b2-507">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="786b2-507">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="786b2-508">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="786b2-508">Service Type</span></span> | <span data-ttu-id="786b2-509">Período de duración</span><span class="sxs-lookup"><span data-stu-id="786b2-509">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="786b2-510">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-510">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="786b2-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="786b2-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="786b2-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="786b2-514">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-514">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="786b2-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="786b2-516">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-516">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="786b2-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="786b2-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="786b2-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="786b2-520">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-520">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="786b2-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="786b2-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="786b2-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-523">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="786b2-524">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="786b2-524">Register additional services with extension methods</span></span>

<span data-ttu-id="786b2-525">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-525">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="786b2-526">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="786b2-526">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="786b2-527">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="786b2-527">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="786b2-528">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="786b2-528">Service lifetimes</span></span>

<span data-ttu-id="786b2-529">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="786b2-529">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="786b2-530">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-530">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="786b2-531">Transitorio</span><span class="sxs-lookup"><span data-stu-id="786b2-531">Transient</span></span>

<span data-ttu-id="786b2-532">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="786b2-532">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="786b2-533">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="786b2-533">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="786b2-534">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-534">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="786b2-535">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="786b2-535">Scoped</span></span>

<span data-ttu-id="786b2-536">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="786b2-536">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="786b2-537">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="786b2-537">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="786b2-538">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="786b2-538">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="786b2-539">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-539">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="786b2-540">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="786b2-540">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="786b2-541">Singleton</span><span class="sxs-lookup"><span data-stu-id="786b2-541">Singleton</span></span>

<span data-ttu-id="786b2-542">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-542">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="786b2-543">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="786b2-543">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="786b2-544">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-544">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="786b2-545">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="786b2-545">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="786b2-546">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-546">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="786b2-547">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-547">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="786b2-548">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="786b2-548">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="786b2-549">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="786b2-549">Service registration methods</span></span>

<span data-ttu-id="786b2-550">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="786b2-550">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="786b2-551">Método</span><span class="sxs-lookup"><span data-stu-id="786b2-551">Method</span></span> | <span data-ttu-id="786b2-552">Automático</span><span class="sxs-lookup"><span data-stu-id="786b2-552">Automatic</span></span><br><span data-ttu-id="786b2-553">objeto</span><span class="sxs-lookup"><span data-stu-id="786b2-553">object</span></span><br><span data-ttu-id="786b2-554">eliminación</span><span class="sxs-lookup"><span data-stu-id="786b2-554">disposal</span></span> | <span data-ttu-id="786b2-555">Múltiple</span><span class="sxs-lookup"><span data-stu-id="786b2-555">Multiple</span></span><br><span data-ttu-id="786b2-556">implementaciones</span><span class="sxs-lookup"><span data-stu-id="786b2-556">implementations</span></span> | <span data-ttu-id="786b2-557">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="786b2-557">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="786b2-558">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="786b2-558">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="786b2-559">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-559">Yes</span></span> | <span data-ttu-id="786b2-560">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-560">Yes</span></span> | <span data-ttu-id="786b2-561">No</span><span class="sxs-lookup"><span data-stu-id="786b2-561">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-562">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-562">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="786b2-563">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-563">Yes</span></span> | <span data-ttu-id="786b2-564">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-564">Yes</span></span> | <span data-ttu-id="786b2-565">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-565">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="786b2-566">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="786b2-566">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="786b2-567">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-567">Yes</span></span> | <span data-ttu-id="786b2-568">No</span><span class="sxs-lookup"><span data-stu-id="786b2-568">No</span></span> | <span data-ttu-id="786b2-569">No</span><span class="sxs-lookup"><span data-stu-id="786b2-569">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-570">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="786b2-571">No</span><span class="sxs-lookup"><span data-stu-id="786b2-571">No</span></span> | <span data-ttu-id="786b2-572">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-572">Yes</span></span> | <span data-ttu-id="786b2-573">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-573">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="786b2-574">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="786b2-574">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="786b2-575">No</span><span class="sxs-lookup"><span data-stu-id="786b2-575">No</span></span> | <span data-ttu-id="786b2-576">No</span><span class="sxs-lookup"><span data-stu-id="786b2-576">No</span></span> | <span data-ttu-id="786b2-577">Sí</span><span class="sxs-lookup"><span data-stu-id="786b2-577">Yes</span></span> |

<span data-ttu-id="786b2-578">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="786b2-578">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="786b2-579">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="786b2-579">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="786b2-580">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="786b2-580">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="786b2-581">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="786b2-581">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="786b2-582">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="786b2-582">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="786b2-583">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="786b2-583">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="786b2-584">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="786b2-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="786b2-585">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-585">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="786b2-586">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="786b2-586">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="786b2-587">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="786b2-587">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="786b2-588">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="786b2-588">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="786b2-589">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="786b2-589">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="786b2-590">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="786b2-590">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="786b2-591">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="786b2-591">Constructor injection behavior</span></span>

<span data-ttu-id="786b2-592">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="786b2-592">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="786b2-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="786b2-594">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="786b2-594">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="786b2-595">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="786b2-595">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="786b2-596">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="786b2-596">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="786b2-597">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="786b2-597">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="786b2-598">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-598">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="786b2-599">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="786b2-599">Entity Framework contexts</span></span>

<span data-ttu-id="786b2-600">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-600">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="786b2-601">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="786b2-601">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="786b2-602">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-602">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="786b2-603">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="786b2-603">Lifetime and registration options</span></span>

<span data-ttu-id="786b2-604">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="786b2-604">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="786b2-605">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="786b2-605">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="786b2-606">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="786b2-606">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="786b2-607">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="786b2-607">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="786b2-608">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="786b2-608">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="786b2-609">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="786b2-609">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="786b2-610">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="786b2-610">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="786b2-611">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="786b2-611">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="786b2-612">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="786b2-612">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="786b2-613">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-613">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="786b2-614">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="786b2-614">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="786b2-615">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-615">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="786b2-616">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="786b2-616">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="786b2-617">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="786b2-617">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="786b2-618">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="786b2-618">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="786b2-619">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="786b2-619">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="786b2-620">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="786b2-620">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="786b2-621">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="786b2-621">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="786b2-622">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="786b2-622">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="786b2-623">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="786b2-623">**First request:**</span></span>

<span data-ttu-id="786b2-624">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="786b2-624">Controller operations:</span></span>

<span data-ttu-id="786b2-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="786b2-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="786b2-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="786b2-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="786b2-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="786b2-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="786b2-628">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="786b2-628">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="786b2-629">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="786b2-629">`OperationService` operations:</span></span>

<span data-ttu-id="786b2-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="786b2-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="786b2-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="786b2-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="786b2-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="786b2-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="786b2-633">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="786b2-633">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="786b2-634">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="786b2-634">**Second request:**</span></span>

<span data-ttu-id="786b2-635">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="786b2-635">Controller operations:</span></span>

<span data-ttu-id="786b2-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="786b2-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="786b2-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="786b2-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="786b2-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="786b2-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="786b2-639">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="786b2-639">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="786b2-640">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="786b2-640">`OperationService` operations:</span></span>

<span data-ttu-id="786b2-641">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="786b2-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="786b2-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="786b2-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="786b2-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="786b2-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="786b2-644">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="786b2-644">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="786b2-645">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="786b2-645">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="786b2-646">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="786b2-646">*Transient* objects are always different.</span></span> <span data-ttu-id="786b2-647">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-647">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="786b2-648">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-648">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="786b2-649">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="786b2-649">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="786b2-650">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-650">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="786b2-651">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="786b2-651">Call services from main</span></span>

<span data-ttu-id="786b2-652">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-652">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="786b2-653">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="786b2-653">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="786b2-654">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="786b2-654">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="786b2-655">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="786b2-655">Scope validation</span></span>

<span data-ttu-id="786b2-656">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="786b2-656">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="786b2-657">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="786b2-657">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="786b2-658">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="786b2-658">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="786b2-659">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="786b2-659">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="786b2-660">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="786b2-660">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="786b2-661">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="786b2-661">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="786b2-662">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="786b2-662">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="786b2-663">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="786b2-663">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="786b2-664">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="786b2-664">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="786b2-665">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="786b2-665">Request Services</span></span>

<span data-ttu-id="786b2-666">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="786b2-666">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="786b2-667">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-667">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="786b2-668">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-668">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="786b2-669">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="786b2-669">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="786b2-670">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-670">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="786b2-671">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="786b2-671">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="786b2-672">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="786b2-672">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="786b2-673">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="786b2-673">Design services for dependency injection</span></span>

<span data-ttu-id="786b2-674">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="786b2-674">Best practices are to:</span></span>

* <span data-ttu-id="786b2-675">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-675">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="786b2-676">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="786b2-676">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="786b2-677">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="786b2-677">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="786b2-678">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-678">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="786b2-679">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="786b2-679">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="786b2-680">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="786b2-680">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="786b2-681">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="786b2-681">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="786b2-682">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="786b2-682">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="786b2-683">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="786b2-683">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="786b2-684">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="786b2-684">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="786b2-685">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="786b2-685">Disposal of services</span></span>

<span data-ttu-id="786b2-686">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="786b2-686">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="786b2-687">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="786b2-687">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="786b2-688">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="786b2-688">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="786b2-689">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="786b2-689">In the following example:</span></span>

* <span data-ttu-id="786b2-690">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-690">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="786b2-691">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="786b2-691">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="786b2-692">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="786b2-692">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="786b2-693">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="786b2-693">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="786b2-694">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="786b2-694">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="786b2-695">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="786b2-695">Transient, limited lifetime</span></span>

<span data-ttu-id="786b2-696">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="786b2-696">**Scenario**</span></span>

<span data-ttu-id="786b2-697">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="786b2-697">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="786b2-698">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="786b2-698">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="786b2-699">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="786b2-699">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="786b2-700">**Solución**</span><span class="sxs-lookup"><span data-stu-id="786b2-700">**Solution**</span></span>

<span data-ttu-id="786b2-701">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="786b2-701">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="786b2-702">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="786b2-702">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="786b2-703">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="786b2-703">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="786b2-704">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="786b2-704">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="786b2-705">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-705">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="786b2-706">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="786b2-706">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="786b2-707">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="786b2-707">**Scenario**</span></span>

<span data-ttu-id="786b2-708">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="786b2-708">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="786b2-709">**Solución**</span><span class="sxs-lookup"><span data-stu-id="786b2-709">**Solution**</span></span>

<span data-ttu-id="786b2-710">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="786b2-710">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="786b2-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="786b2-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="786b2-712">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="786b2-712">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="786b2-713">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="786b2-713">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="786b2-714">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="786b2-714">General Guidelines</span></span>

* <span data-ttu-id="786b2-715">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="786b2-715">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="786b2-716">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="786b2-716">Use the factory pattern instead.</span></span>
* <span data-ttu-id="786b2-717">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="786b2-717">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="786b2-718">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="786b2-718">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="786b2-719">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="786b2-719">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="786b2-720">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="786b2-720">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="786b2-721">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-721">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="786b2-722">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="786b2-722">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="786b2-723">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="786b2-723">Default service container replacement</span></span>

<span data-ttu-id="786b2-724">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="786b2-724">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="786b2-725">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="786b2-725">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="786b2-726">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="786b2-726">Property injection</span></span>
* <span data-ttu-id="786b2-727">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="786b2-727">Injection based on name</span></span>
* <span data-ttu-id="786b2-728">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="786b2-728">Child containers</span></span>
* <span data-ttu-id="786b2-729">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="786b2-729">Custom lifetime management</span></span>
* <span data-ttu-id="786b2-730">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="786b2-730">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="786b2-731">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="786b2-731">Convention-based registration</span></span>

<span data-ttu-id="786b2-732">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="786b2-732">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="786b2-733">Autofac</span><span class="sxs-lookup"><span data-stu-id="786b2-733">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="786b2-734">DryIoc</span><span class="sxs-lookup"><span data-stu-id="786b2-734">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="786b2-735">Grace</span><span class="sxs-lookup"><span data-stu-id="786b2-735">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="786b2-736">LightInject</span><span class="sxs-lookup"><span data-stu-id="786b2-736">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="786b2-737">Lamar</span><span class="sxs-lookup"><span data-stu-id="786b2-737">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="786b2-738">Stashbox</span><span class="sxs-lookup"><span data-stu-id="786b2-738">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="786b2-739">Unity</span><span class="sxs-lookup"><span data-stu-id="786b2-739">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="786b2-740">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="786b2-740">Thread safety</span></span>

<span data-ttu-id="786b2-741">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="786b2-741">Create thread-safe singleton services.</span></span> <span data-ttu-id="786b2-742">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="786b2-742">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="786b2-743">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="786b2-743">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="786b2-744">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="786b2-744">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="786b2-745">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="786b2-745">Recommendations</span></span>

* <span data-ttu-id="786b2-746">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="786b2-746">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="786b2-747">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="786b2-747">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="786b2-748">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-748">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="786b2-749">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-749">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="786b2-750">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="786b2-750">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="786b2-751">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="786b2-751">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="786b2-752">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="786b2-752">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="786b2-753">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="786b2-753">Avoid static access to services.</span></span> <span data-ttu-id="786b2-754">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="786b2-754">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="786b2-755">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="786b2-755">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="786b2-756">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="786b2-756">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="786b2-757">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="786b2-757">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="786b2-758">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="786b2-758">**Correct**:</span></span>

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

* <span data-ttu-id="786b2-759">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="786b2-759">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="786b2-760">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="786b2-760">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="786b2-761">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="786b2-761">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="786b2-762">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="786b2-762">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="786b2-763">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="786b2-763">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="786b2-764">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="786b2-764">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="786b2-765">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="786b2-765">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="786b2-766">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="786b2-766">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="786b2-767">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="786b2-767">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="786b2-768">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="786b2-768">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="786b2-769">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="786b2-769">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="786b2-770">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="786b2-770">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
