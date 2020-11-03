---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678568"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="ac16f-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac16f-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac16f-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="ac16f-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="ac16f-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ac16f-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ac16f-107">Para obtener información sobre el uso de la inserción de dependencias en aplicaciones de consola, vea [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ac16f-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="ac16f-108">Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ac16f-109">En este tema se proporciona información sobre la inyección de dependencias en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac16f-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="ac16f-110">Para obtener información sobre el uso de la inyección de dependencias en las aplicaciones de consola, consulte [Inyección de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ac16f-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="ac16f-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac16f-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ac16f-112">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-112">Overview of dependency injection</span></span>

<span data-ttu-id="ac16f-113">Una *dependencia* es un objeto del que depende otro objeto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="ac16f-114">Examine la clase `MyDependency` siguiente con un método `WriteMessage` del que dependen otras clases:</span><span class="sxs-lookup"><span data-stu-id="ac16f-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="ac16f-115">Una clase puede crear una instancia de la clase `MyDependency` para usar su método `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="ac16f-116">En el ejemplo siguiente, la clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ac16f-117">La clase crea y depende directamente de la clase `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="ac16f-118">Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las razones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ac16f-119">Para reemplazar `MyDependency` por una implementación diferente, se debe modificar la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="ac16f-120">Si `MyDependency` tiene dependencias, deben configurarse según la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="ac16f-121">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ac16f-122">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ac16f-123">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ac16f-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ac16f-124">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="ac16f-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ac16f-125">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ac16f-126">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ac16f-127">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ac16f-128">Por lo general, los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ac16f-129">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="ac16f-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ac16f-130">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="ac16f-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ac16f-131">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define el método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ac16f-132">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ac16f-133">La aplicación de ejemplo registra el servicio `IMyDependency` con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ac16f-134">El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="ac16f-135">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="ac16f-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="ac16f-136">En la aplicación de ejemplo, el servicio `IMyDependency` se solicita y usa para llamar al método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="ac16f-137">Mediante el uso del patrón de DI, el controlador:</span><span class="sxs-lookup"><span data-stu-id="ac16f-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="ac16f-138">No usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency` que implementa.</span><span class="sxs-lookup"><span data-stu-id="ac16f-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="ac16f-139">Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.</span><span class="sxs-lookup"><span data-stu-id="ac16f-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="ac16f-140">No crea una instancia de `MyDependency`, la crea el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="ac16f-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="ac16f-141">La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:</span><span class="sxs-lookup"><span data-stu-id="ac16f-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="ac16f-142">El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="ac16f-143">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601>, el que solicita en el constructor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="ac16f-144">`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="ac16f-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="ac16f-145">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ac16f-146">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ac16f-147">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ac16f-148">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias* , *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="ac16f-149">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="ac16f-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="ac16f-150">En la terminología de la inserción de dependencias, un servicio:</span><span class="sxs-lookup"><span data-stu-id="ac16f-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="ac16f-151">Por lo general, es un objeto que proporciona un servicio a otros objetos, como el servicio `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="ac16f-152">No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.</span><span class="sxs-lookup"><span data-stu-id="ac16f-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="ac16f-153">El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido.</span><span class="sxs-lookup"><span data-stu-id="ac16f-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="ac16f-154">Las implementaciones de `IMyDependency` que se muestran en los ejemplos anteriores se escribieron para mostrar la inserción de DI básica, no para implementar el registro.</span><span class="sxs-lookup"><span data-stu-id="ac16f-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="ac16f-155">La mayoría de las aplicaciones no deberían tener que escribir registradores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="ac16f-156">En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="ac16f-157">Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="ac16f-158">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="ac16f-158">Services injected into Startup</span></span>

<span data-ttu-id="ac16f-159">Los servicios se pueden insertar en el constructor `Startup` y en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="ac16f-160">Solo se pueden insertar los servicios siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="ac16f-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ac16f-161">Cualquier servicio registrado con el contenedor de DI se puede insertar en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="ac16f-162">Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="ac16f-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="ac16f-163">Registro de grupos de servicios con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="ac16f-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="ac16f-164">El marco ASP.NET Core usa una convención para registrar un grupo de servicios relacionados.</span><span class="sxs-lookup"><span data-stu-id="ac16f-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="ac16f-165">La convención es usar un único método de extensión de `Add{GROUP_NAME}` para registrar todos los servicios requeridos por una característica de marco.</span><span class="sxs-lookup"><span data-stu-id="ac16f-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="ac16f-166">Por ejemplo, el método de extensión <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> registra los servicios necesarios para los controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="ac16f-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="ac16f-167">El código siguiente lo genera la plantilla de :::no-loc(Razor)::: Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span><span class="sxs-lookup"><span data-stu-id="ac16f-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="ac16f-168">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="ac16f-168">Service lifetimes</span></span>

<span data-ttu-id="ac16f-169">Los servicios se pueden registrar con una de las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="ac16f-170">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-170">Transient</span></span>
* <span data-ttu-id="ac16f-171">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="ac16f-171">Scoped</span></span>
* <span data-ttu-id="ac16f-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-172">Singleton</span></span>

<span data-ttu-id="ac16f-173">En las secciones siguientes se describen cada una de las duraciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="ac16f-174">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="ac16f-175">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-175">Transient</span></span>

<span data-ttu-id="ac16f-176">Los servicios de duración transitoria se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="ac16f-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="ac16f-177">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="ac16f-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="ac16f-178">Registre los servicios transitorios con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="ac16f-179">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="ac16f-180">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="ac16f-180">Scoped</span></span>

<span data-ttu-id="ac16f-181">Los servicios de duración con ámbito se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="ac16f-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="ac16f-182">Registre los servicios con ámbito con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="ac16f-183">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="ac16f-184">Cuando se usa Entity Framework Core, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> registra tipos de `DbContext` con una duración de ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="ac16f-185">**No** resuelva un servicio con ámbito desde un singleton y tenga cuidado de no hacerlo indirectamente, por ejemplo, a través de un servicio transitorio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="ac16f-186">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="ac16f-187">Basta con:</span><span class="sxs-lookup"><span data-stu-id="ac16f-187">It's fine to:</span></span>

<span data-ttu-id="ac16f-188">Resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="ac16f-189">Resolver un servicio con ámbito desde otro servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="ac16f-190">De manera predeterminada, en el entorno de desarrollo, resolver un servicio desde otro servicio con una duración más larga genera una excepción.</span><span class="sxs-lookup"><span data-stu-id="ac16f-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="ac16f-191">Para más información, vea [Validación del ámbito](#sv).</span><span class="sxs-lookup"><span data-stu-id="ac16f-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="ac16f-192">Para usar servicios con ámbito en middleware, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="ac16f-193">Inserte el servicio en el método `Invoke` o `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="ac16f-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ac16f-194">El uso de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) genera una excepción en tiempo de ejecución porque obliga al servicio con ámbito a comportarse como un singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="ac16f-195">El ejemplo que aparece en la sección [Opciones de registro y duración](#lifetime-and-registration-options) muestra el enfoque `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="ac16f-196">Use [middleware basado en Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="ac16f-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="ac16f-197">El middleware registrado mediante este enfoque se activa por solicitud de cliente (conexión), lo que permite que los servicios con ámbito se inserten en el método `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="ac16f-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="ac16f-198">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ac16f-199">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-199">Singleton</span></span>

<span data-ttu-id="ac16f-200">Los servicios de duración de singleton se crean de alguna de las formas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="ac16f-201">La primera vez que se solicitan.</span><span class="sxs-lookup"><span data-stu-id="ac16f-201">The first time they're requested.</span></span>
* <span data-ttu-id="ac16f-202">Mediante el desarrollador, al proporcionar una instancia de implementación directamente al contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="ac16f-203">Este enfoque rara vez es necesario.</span><span class="sxs-lookup"><span data-stu-id="ac16f-203">This approach is rarely needed.</span></span>

<span data-ttu-id="ac16f-204">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="ac16f-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ac16f-205">Si la aplicación requiere un comportamiento de singleton, permita que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="ac16f-206">No implemente el modelo de diseño singleton y proporcione el código para desechar el singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="ac16f-207">Los servicios nunca deben desecharse mediante el código que haya resuelto el servicio del contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="ac16f-208">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="ac16f-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="ac16f-209">Registre los servicios singleton con <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="ac16f-210">Los servicios singleton deben ser seguros para los subprocesos y se suelen usar en servicios sin estado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="ac16f-211">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="ac16f-212">Como no se libera memoria hasta que se apaga la aplicación, se debe tener en cuenta el uso de memoria con un servicio singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="ac16f-213">**No** resuelva un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ac16f-214">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="ac16f-215">Basta con resolver un servicio singleton desde un servicio con ámbito o transitorio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ac16f-216">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="ac16f-216">Service registration methods</span></span>

<span data-ttu-id="ac16f-217">El marco proporciona métodos de extensión de registro del servicio que resultan útiles en escenarios específicos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="ac16f-218">Método</span><span class="sxs-lookup"><span data-stu-id="ac16f-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="ac16f-219">Automático</span><span class="sxs-lookup"><span data-stu-id="ac16f-219">Automatic</span></span><br><span data-ttu-id="ac16f-220">objeto</span><span class="sxs-lookup"><span data-stu-id="ac16f-220">object</span></span><br><span data-ttu-id="ac16f-221">eliminación</span><span class="sxs-lookup"><span data-stu-id="ac16f-221">disposal</span></span> | <span data-ttu-id="ac16f-222">Múltiple</span><span class="sxs-lookup"><span data-stu-id="ac16f-222">Multiple</span></span><br><span data-ttu-id="ac16f-223">implementaciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-223">implementations</span></span> | <span data-ttu-id="ac16f-224">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="ac16f-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ac16f-225">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac16f-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="ac16f-226">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-226">Yes</span></span>                             | <span data-ttu-id="ac16f-227">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-227">Yes</span></span>                         | <span data-ttu-id="ac16f-228">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-229">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="ac16f-230">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-230">Yes</span></span>                             | <span data-ttu-id="ac16f-231">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-231">Yes</span></span>                         | <span data-ttu-id="ac16f-232">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ac16f-233">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac16f-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="ac16f-234">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-234">Yes</span></span>                             | <span data-ttu-id="ac16f-235">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-235">No</span></span>                          | <span data-ttu-id="ac16f-236">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-237">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="ac16f-238">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-238">No</span></span>                              | <span data-ttu-id="ac16f-239">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-239">Yes</span></span>                         | <span data-ttu-id="ac16f-240">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-241">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="ac16f-242">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-242">No</span></span>                              | <span data-ttu-id="ac16f-243">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-243">No</span></span>                          | <span data-ttu-id="ac16f-244">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-244">Yes</span></span>       |

<span data-ttu-id="ac16f-245">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="ac16f-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ac16f-246">Es habitual usar varias implementaciones al [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ac16f-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ac16f-247">El registro de un servicio con un solo tipo de implementación es equivalente al registro de ese servicio con la misma implementación y el mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="ac16f-248">Por eso no se pueden registrar varias implementaciones de un servicio mediante los métodos que no toman un tipo de servicio explícito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="ac16f-249">Estos métodos pueden registrar varias instancias de un servicio, pero todos tienen el mismo tipo de *implementación*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="ac16f-250">Cualquiera de los métodos de registro de servicio anteriores se puede usar para registrar varias instancias de servicio del mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="ac16f-251">En el ejemplo siguiente se llama a `AddSingleton` dos veces con `IMyDependency` como tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="ac16f-252">La segunda llamada a `AddSingleton` invalida la anterior cuando se resuelve como `IMyDependency`, y se agrega a la anterior cuando varios servicios se resuelven mediante `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="ac16f-253">Los servicios aparecen en el orden en que se han registrado al resolverse mediante `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="ac16f-254">El marco también proporciona métodos de extensión `TryAdd{LIFETIME}`, que registran el servicio solo si todavía no hay registrada una implementación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ac16f-255">En el ejemplo siguiente, la llamada a `AddSingleton` registra `MyDependency` como una implementación para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="ac16f-256">La llamada a `TryAddSingleton` no tiene ningún efecto, puesto que `IMyDependency` ya tiene una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="ac16f-257">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="ac16f-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="ac16f-258">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) registran el servicio solo si todavía no hay una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ac16f-259">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ac16f-260">Al registrar los servicios, el desarrollador debería agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ac16f-261">Por lo general, los autores de bibliotecas usan `TryAddEnumerable` para evitar el registro de varias copias de una implementación en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="ac16f-262">En el ejemplo siguiente, la primera llamada a `TryAddEnumerable` registra `MyDependency` como una implementación para `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="ac16f-263">La segunda llamada registra `MyDependency` para `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="ac16f-264">La tercera llamada no tiene ningún efecto porque `IMyDependency1` ya tiene una implementación registrada de `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="ac16f-265">Normalmente, el registro del servicio es independiente, excepto cuando se registran varias implementaciones del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="ac16f-266">`IServiceCollection` es una colección de objetos <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="ac16f-267">En el ejemplo siguiente se muestra cómo registrar un servicio mediante la creación e incorporación de un elemento `ServiceDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="ac16f-268">Los métodos `Add{LIFETIME}` integrados usan el mismo enfoque.</span><span class="sxs-lookup"><span data-stu-id="ac16f-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="ac16f-269">Por ejemplo, consulte el [código fuente de AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="ac16f-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ac16f-270">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="ac16f-270">Constructor injection behavior</span></span>

<span data-ttu-id="ac16f-271">Los servicios se pueden resolver mediante el uso de:</span><span class="sxs-lookup"><span data-stu-id="ac16f-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ac16f-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="ac16f-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="ac16f-273">Crea objetos que no están registrados en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="ac16f-274">Se utiliza con características de marcos, como [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), controladores MVC y [enlazadores de modelos](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ac16f-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="ac16f-275">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="ac16f-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ac16f-276">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ac16f-277">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="ac16f-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ac16f-278">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ac16f-279">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ac16f-279">Entity Framework contexts</span></span>

<span data-ttu-id="ac16f-280">De manera predeterminada, los contextos de Entity Framework se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ac16f-281">Para usar una duración distinta, especifíquela mediante el uso de una sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="ac16f-282">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que la del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ac16f-283">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="ac16f-283">Lifetime and registration options</span></span>

<span data-ttu-id="ac16f-284">Para mostrar la diferencia entre las duraciones del servicio y sus opciones de registro, considere las interfaces siguientes que representan una tarea como una operación con un identificador, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="ac16f-285">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona las mismas instancias del servicio u otras distintas cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="ac16f-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ac16f-286">La clase `Operation` siguiente implementa todas las interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="ac16f-287">El constructor `Operation` genera un GUID y almacena los 4 últimos caracteres en la propiedad `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="ac16f-288">El método `Startup.ConfigureServices` crea varios registros de la clase `Operation` de acuerdo con las duraciones mencionadas:</span><span class="sxs-lookup"><span data-stu-id="ac16f-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ac16f-289">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ac16f-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="ac16f-290">`IndexModel` y el middleware solicitan cada clase del tipo `IOperation` y registran el valor de `OperationId` de cada una:</span><span class="sxs-lookup"><span data-stu-id="ac16f-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="ac16f-291">De manera similar al `IndexModel`, el middleware resuelve los mismos servicios:</span><span class="sxs-lookup"><span data-stu-id="ac16f-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="ac16f-292">Los servicios con ámbito se deben resolver en el método `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="ac16f-293">La salida del registrador muestra:</span><span class="sxs-lookup"><span data-stu-id="ac16f-293">The logger output shows:</span></span>

* <span data-ttu-id="ac16f-294">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="ac16f-294">*Transient* objects are always different.</span></span> <span data-ttu-id="ac16f-295">El valor `OperationId` transitorio es distinto en el `IndexModel` y en el middleware.</span><span class="sxs-lookup"><span data-stu-id="ac16f-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="ac16f-296">Los objetos *con ámbito* son iguales para cada solicitud, pero varían entre solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ac16f-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="ac16f-297">Los objetos *singleton* son los mismos para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="ac16f-298">Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="ac16f-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="ac16f-299">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="ac16f-299">Call services from main</span></span>

<span data-ttu-id="ac16f-300">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ac16f-301">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="ac16f-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="ac16f-302">En el ejemplo siguiente se muestra cómo acceder al servicio `IMyDependency` con ámbito y llamar a su método `WriteMessage` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="ac16f-303">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="ac16f-303">Scope validation</span></span>

<span data-ttu-id="ac16f-304">Cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments) y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac16f-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ac16f-305">Los servicios con ámbito no se resuelven desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="ac16f-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="ac16f-306">Los servicios con ámbito no se insertan en singletons.</span><span class="sxs-lookup"><span data-stu-id="ac16f-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="ac16f-307">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="ac16f-308">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="ac16f-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ac16f-309">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="ac16f-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ac16f-310">Si un servicio con ámbito se crea en el contenedor raíz, su duración sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="ac16f-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="ac16f-311">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ac16f-312">Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="ac16f-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="ac16f-313">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="ac16f-313">Request Services</span></span>

<span data-ttu-id="ac16f-314">Los servicios disponibles en una solicitud de ASP.NET Core se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="ac16f-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="ac16f-315">Cuando se solicitan servicios dentro de una solicitud, los servicios y sus dependencias se resuelven desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="ac16f-316">El marco crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="ac16f-317">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="ac16f-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="ac16f-318">Se recomienda que solicite las dependencias como parámetros del constructor para resolver los servicios desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="ac16f-319">Esto genera clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="ac16f-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ac16f-320">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-320">Design services for dependency injection</span></span>

<span data-ttu-id="ac16f-321">Al diseñar servicios para la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ac16f-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="ac16f-322">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ac16f-323">Evitar crear un estado global mediante el diseño de aplicaciones para usar servicios singleton en su lugar.</span><span class="sxs-lookup"><span data-stu-id="ac16f-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="ac16f-324">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ac16f-325">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="ac16f-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ac16f-326">Cree servicios pequeños, bien factorizados y probados con facilidad.</span><span class="sxs-lookup"><span data-stu-id="ac16f-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ac16f-327">Si una clase tiene muchas dependencias insertadas, podría ser un signo de que la clase tiene demasiadas responsabilidades e infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ac16f-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ac16f-328">Trate de mover algunas de las responsabilidades de la clase a clases nuevas para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="ac16f-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="ac16f-329">Tenga en cuenta que las clases del modelo de página de :::no-loc(Razor)::: Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="ac16f-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ac16f-330">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="ac16f-330">Disposal of services</span></span>

<span data-ttu-id="ac16f-331">El contenedor llama a <xref:System.IDisposable.Dispose%2A> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="ac16f-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ac16f-332">El desarrollador nunca debe eliminar los servicios resueltos desde el contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="ac16f-333">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="ac16f-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="ac16f-334">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="ac16f-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ac16f-335">La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:</span><span class="sxs-lookup"><span data-stu-id="ac16f-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="ac16f-336">Servicios no creados por el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="ac16f-336">Services not created by the service container</span></span>

<span data-ttu-id="ac16f-337">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac16f-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="ac16f-338">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="ac16f-338">In the preceding code:</span></span>

* <span data-ttu-id="ac16f-339">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ac16f-340">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="ac16f-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ac16f-341">El desarrollador es responsable de la eliminación de los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ac16f-342">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="ac16f-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ac16f-343">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="ac16f-343">Transient, limited lifetime</span></span>

<span data-ttu-id="ac16f-344">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="ac16f-344">**Scenario**</span></span>

<span data-ttu-id="ac16f-345">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="ac16f-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="ac16f-346">La instancia se resuelve en el ámbito raíz (contenedor raíz).</span><span class="sxs-lookup"><span data-stu-id="ac16f-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="ac16f-347">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="ac16f-348">**Solución**</span><span class="sxs-lookup"><span data-stu-id="ac16f-348">**Solution**</span></span>

<span data-ttu-id="ac16f-349">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="ac16f-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="ac16f-350">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="ac16f-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="ac16f-351">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="ac16f-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="ac16f-352">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="ac16f-353">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ac16f-354">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="ac16f-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="ac16f-355">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="ac16f-355">**Scenario**</span></span>

<span data-ttu-id="ac16f-356">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero la instancia de <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="ac16f-357">**Solución**</span><span class="sxs-lookup"><span data-stu-id="ac16f-357">**Solution**</span></span>

<span data-ttu-id="ac16f-358">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="ac16f-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="ac16f-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="ac16f-360">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ac16f-361">Elimine el ámbito cuando ya no lo necesite.</span><span class="sxs-lookup"><span data-stu-id="ac16f-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="ac16f-362">Instrucciones generales de IDisposable</span><span class="sxs-lookup"><span data-stu-id="ac16f-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="ac16f-363">No registre instancias de <xref:System.IDisposable> con una duración transitoria.</span><span class="sxs-lookup"><span data-stu-id="ac16f-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="ac16f-364">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="ac16f-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="ac16f-365">No resuelva instancias de <xref:System.IDisposable> con una duración transitoria o restringida en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="ac16f-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="ac16f-366">La única excepción a esto es si la aplicación crea o vuelve a crear y elimina <xref:System.IServiceProvider>, pero este no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="ac16f-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="ac16f-367">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ac16f-368">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="ac16f-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="ac16f-369">Use ámbitos para controlar las duraciones de los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="ac16f-370">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ac16f-371">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="ac16f-371">Default service container replacement</span></span>

<span data-ttu-id="ac16f-372">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ac16f-373">Se recomienda usar el contenedor integrado a menos que se necesite una característica específica no admitida por el contenedor, como:</span><span class="sxs-lookup"><span data-stu-id="ac16f-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="ac16f-374">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="ac16f-374">Property injection</span></span>
* <span data-ttu-id="ac16f-375">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="ac16f-375">Injection based on name</span></span>
* <span data-ttu-id="ac16f-376">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="ac16f-376">Child containers</span></span>
* <span data-ttu-id="ac16f-377">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ac16f-377">Custom lifetime management</span></span>
* <span data-ttu-id="ac16f-378">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="ac16f-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ac16f-379">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-379">Convention-based registration</span></span>

<span data-ttu-id="ac16f-380">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ac16f-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="ac16f-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="ac16f-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ac16f-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ac16f-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="ac16f-383">Grace</span><span class="sxs-lookup"><span data-stu-id="ac16f-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="ac16f-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="ac16f-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="ac16f-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="ac16f-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="ac16f-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ac16f-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="ac16f-387">Unity</span><span class="sxs-lookup"><span data-stu-id="ac16f-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="ac16f-388">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="ac16f-388">Thread safety</span></span>

<span data-ttu-id="ac16f-389">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="ac16f-390">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="ac16f-391">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ac16f-392">Al igual que un constructor de tipos (`static`), se garantiza que se le llame solo una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="ac16f-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ac16f-393">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-393">Recommendations</span></span>

* <span data-ttu-id="ac16f-394">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="ac16f-395">Como C# no admite constructores asincrónicos, use los métodos asincrónicos después de resolver sincrónicamente el servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="ac16f-396">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ac16f-397">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ac16f-398">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac16f-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ac16f-399">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="ac16f-400">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="ac16f-401">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-401">Avoid static access to services.</span></span> <span data-ttu-id="ac16f-402">Por ejemplo, evite capturar [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) como campo estático o propiedad para su uso en otra parte.</span><span class="sxs-lookup"><span data-stu-id="ac16f-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="ac16f-403">Mantenga los generadores de DI rápidos y sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="ac16f-404">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="ac16f-405">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService%2A> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="ac16f-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="ac16f-406">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="ac16f-406">**Incorrect:**</span></span>

    ![Código incorrecto](dependency-injection/_static/bad.png)

  <span data-ttu-id="ac16f-408">**Correcto** :</span><span class="sxs-lookup"><span data-stu-id="ac16f-408">**Correct** :</span></span>

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
* <span data-ttu-id="ac16f-409">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ac16f-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="ac16f-410">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="ac16f-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="ac16f-411">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ac16f-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="ac16f-412">Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="ac16f-413">La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="ac16f-414">Por ejemplo, considere el caso donde `LoginPath` se carga desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="ac16f-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="ac16f-415">Evite el enfoque siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac16f-415">Avoid the following approach:</span></span>

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="ac16f-417">En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:</span><span class="sxs-lookup"><span data-stu-id="ac16f-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="ac16f-418">ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="ac16f-419">Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".</span><span class="sxs-lookup"><span data-stu-id="ac16f-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="ac16f-420">La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="ac16f-421">Una manera correcta de obtener `LoginPath` es usar la compatibilidad integrada del patrón de opciones para DI:</span><span class="sxs-lookup"><span data-stu-id="ac16f-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="ac16f-422">El contenedor captura a los servicios transitorios descartables para su eliminación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="ac16f-423">Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="ac16f-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="ac16f-424">Habilite la validación con ámbito para asegurarse de que la aplicación no tenga singletons que capturen los servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="ac16f-425">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="ac16f-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="ac16f-426">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="ac16f-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ac16f-427">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ac16f-428">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="ac16f-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ac16f-429">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="ac16f-430">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="ac16f-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) es un marco de trabajo de la aplicación para compilar aplicaciones modulares y multiinquilino en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac16f-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="ac16f-432">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="ac16f-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="ac16f-433">Consulte los [ejemplos de Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="ac16f-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ac16f-434">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="ac16f-434">Framework-provided services</span></span>

<span data-ttu-id="ac16f-435">El método `Startup.ConfigureServices` registra los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ac16f-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ac16f-436">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ac16f-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ac16f-437">En el caso de las aplicaciones basadas en las plantillas de ASP.NET Core, el marco de trabajo registra más de 250 servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="ac16f-438">En la tabla siguiente se ilustra una pequeña muestra de estos servicios registrados por el marco:</span><span class="sxs-lookup"><span data-stu-id="ac16f-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="ac16f-439">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="ac16f-439">Service Type</span></span>                                                                                    | <span data-ttu-id="ac16f-440">Período de duración</span><span class="sxs-lookup"><span data-stu-id="ac16f-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ac16f-441">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="ac16f-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="ac16f-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="ac16f-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="ac16f-445">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="ac16f-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="ac16f-447">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="ac16f-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="ac16f-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="ac16f-450">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="ac16f-451">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="ac16f-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="ac16f-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="ac16f-454">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="ac16f-455">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ac16f-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="ac16f-456">Patrones de la Conferencia NDC para el desarrollo de aplicaciones de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ac16f-457">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac16f-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="ac16f-458">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ac16f-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="ac16f-459">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="ac16f-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ac16f-460">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="ac16f-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="ac16f-461">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac16f-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac16f-462">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="ac16f-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="ac16f-463">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ac16f-464">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ac16f-465">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac16f-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ac16f-466">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-466">Overview of dependency injection</span></span>

<span data-ttu-id="ac16f-467">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="ac16f-468">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="ac16f-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="ac16f-469">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="ac16f-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ac16f-470">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ac16f-471">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ac16f-472">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="ac16f-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ac16f-473">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="ac16f-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="ac16f-474">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="ac16f-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="ac16f-475">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ac16f-476">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ac16f-477">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="ac16f-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ac16f-478">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="ac16f-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ac16f-479">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ac16f-480">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ac16f-481">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ac16f-482">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ac16f-483">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="ac16f-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ac16f-484">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="ac16f-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ac16f-485">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ac16f-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ac16f-486">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ac16f-487">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ac16f-488">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ac16f-489">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ac16f-490">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ac16f-491">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias* , *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="ac16f-492">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ac16f-493">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac16f-494">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="ac16f-495">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="ac16f-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ac16f-496">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ac16f-497">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="ac16f-498">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="ac16f-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ac16f-499">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="ac16f-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ac16f-500">Por ejemplo, `services.AddMvc()` agrega los servicios que :::no-loc(Razor)::: Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="ac16f-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="ac16f-501">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="ac16f-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="ac16f-502">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ac16f-503">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="ac16f-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="ac16f-504">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="ac16f-505">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="ac16f-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="ac16f-506">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="ac16f-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ac16f-507">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="ac16f-507">Services injected into Startup</span></span>

<span data-ttu-id="ac16f-508">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="ac16f-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ac16f-509">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ac16f-510">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ac16f-511">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="ac16f-511">Framework-provided services</span></span>

<span data-ttu-id="ac16f-512">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ac16f-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ac16f-513">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ac16f-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ac16f-514">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="ac16f-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="ac16f-515">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="ac16f-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="ac16f-516">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="ac16f-516">Service Type</span></span> | <span data-ttu-id="ac16f-517">Período de duración</span><span class="sxs-lookup"><span data-stu-id="ac16f-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ac16f-518">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="ac16f-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="ac16f-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ac16f-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ac16f-522">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ac16f-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ac16f-524">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ac16f-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ac16f-526">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ac16f-527">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ac16f-528">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ac16f-529">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ac16f-530">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ac16f-531">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ac16f-532">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="ac16f-532">Register additional services with extension methods</span></span>

<span data-ttu-id="ac16f-533">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="ac16f-534">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span><span class="sxs-lookup"><span data-stu-id="ac16f-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="ac16f-535">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="ac16f-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ac16f-536">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="ac16f-536">Service lifetimes</span></span>

<span data-ttu-id="ac16f-537">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="ac16f-538">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="ac16f-539">Transitorio</span><span class="sxs-lookup"><span data-stu-id="ac16f-539">Transient</span></span>

<span data-ttu-id="ac16f-540">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="ac16f-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ac16f-541">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="ac16f-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="ac16f-542">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="ac16f-543">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="ac16f-543">Scoped</span></span>

<span data-ttu-id="ac16f-544">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="ac16f-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="ac16f-545">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ac16f-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="ac16f-546">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ac16f-547">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ac16f-548">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ac16f-549">Singleton</span><span class="sxs-lookup"><span data-stu-id="ac16f-549">Singleton</span></span>

<span data-ttu-id="ac16f-550">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="ac16f-551">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="ac16f-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ac16f-552">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="ac16f-553">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="ac16f-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="ac16f-554">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="ac16f-555">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ac16f-556">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="ac16f-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ac16f-557">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="ac16f-557">Service registration methods</span></span>

<span data-ttu-id="ac16f-558">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="ac16f-559">Método</span><span class="sxs-lookup"><span data-stu-id="ac16f-559">Method</span></span> | <span data-ttu-id="ac16f-560">Automático</span><span class="sxs-lookup"><span data-stu-id="ac16f-560">Automatic</span></span><br><span data-ttu-id="ac16f-561">objeto</span><span class="sxs-lookup"><span data-stu-id="ac16f-561">object</span></span><br><span data-ttu-id="ac16f-562">eliminación</span><span class="sxs-lookup"><span data-stu-id="ac16f-562">disposal</span></span> | <span data-ttu-id="ac16f-563">Múltiple</span><span class="sxs-lookup"><span data-stu-id="ac16f-563">Multiple</span></span><br><span data-ttu-id="ac16f-564">implementaciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-564">implementations</span></span> | <span data-ttu-id="ac16f-565">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="ac16f-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ac16f-566">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac16f-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ac16f-567">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-567">Yes</span></span> | <span data-ttu-id="ac16f-568">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-568">Yes</span></span> | <span data-ttu-id="ac16f-569">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-570">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ac16f-571">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-571">Yes</span></span> | <span data-ttu-id="ac16f-572">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-572">Yes</span></span> | <span data-ttu-id="ac16f-573">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ac16f-574">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ac16f-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ac16f-575">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-575">Yes</span></span> | <span data-ttu-id="ac16f-576">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-576">No</span></span> | <span data-ttu-id="ac16f-577">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-578">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ac16f-579">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-579">No</span></span> | <span data-ttu-id="ac16f-580">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-580">Yes</span></span> | <span data-ttu-id="ac16f-581">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ac16f-582">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ac16f-583">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-583">No</span></span> | <span data-ttu-id="ac16f-584">No</span><span class="sxs-lookup"><span data-stu-id="ac16f-584">No</span></span> | <span data-ttu-id="ac16f-585">Sí</span><span class="sxs-lookup"><span data-stu-id="ac16f-585">Yes</span></span> |

<span data-ttu-id="ac16f-586">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="ac16f-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ac16f-587">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ac16f-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ac16f-588">El registro de un servicio con un solo tipo de implementación es equivalente al registro de ese servicio con la misma implementación y el mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="ac16f-589">Por eso no se pueden registrar varias implementaciones de un servicio mediante los métodos que no toman un tipo de servicio explícito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="ac16f-590">Estos métodos pueden registrar varias *instancias* de un servicio, pero todos tienen el mismo tipo de *implementación*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="ac16f-591">Cualquiera de los métodos de registro de servicio anteriores se puede usar para registrar varias instancias de servicio del mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="ac16f-592">En el ejemplo siguiente se llama a `AddSingleton` dos veces con `IMyDependency` como tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="ac16f-593">La segunda llamada a `AddSingleton` invalida la anterior cuando se resuelve como `IMyDependency`, y se agrega a la anterior cuando varios servicios se resuelven mediante `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="ac16f-594">Los servicios aparecen en el orden en que se han registrado al resolverse mediante `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="ac16f-595">El marco también proporciona métodos de extensión `TryAdd{LIFETIME}`, que registran el servicio solo si todavía no hay registrada una implementación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ac16f-596">En el ejemplo siguiente, la llamada a `AddSingleton` registra `MyDependency` como una implementación para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="ac16f-597">La llamada a `TryAddSingleton` no tiene ningún efecto, puesto que `IMyDependency` ya tiene una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="ac16f-598">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="ac16f-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ac16f-599">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ac16f-600">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ac16f-601">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ac16f-602">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="ac16f-603">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ac16f-604">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="ac16f-605">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ac16f-606">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="ac16f-606">Constructor injection behavior</span></span>

<span data-ttu-id="ac16f-607">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="ac16f-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ac16f-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="ac16f-609">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ac16f-610">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="ac16f-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ac16f-611">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="ac16f-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ac16f-612">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="ac16f-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ac16f-613">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ac16f-614">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ac16f-614">Entity Framework contexts</span></span>

<span data-ttu-id="ac16f-615">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ac16f-616">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ac16f-617">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ac16f-618">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="ac16f-618">Lifetime and registration options</span></span>

<span data-ttu-id="ac16f-619">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="ac16f-620">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="ac16f-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ac16f-621">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ac16f-622">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="ac16f-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ac16f-623">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ac16f-624">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="ac16f-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="ac16f-625">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="ac16f-626">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="ac16f-627">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="ac16f-628">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="ac16f-629">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="ac16f-630">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ac16f-631">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="ac16f-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ac16f-632">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="ac16f-633">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="ac16f-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ac16f-634">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="ac16f-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="ac16f-635">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="ac16f-636">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="ac16f-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ac16f-637">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="ac16f-638">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="ac16f-638">**First request:**</span></span>

<span data-ttu-id="ac16f-639">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="ac16f-639">Controller operations:</span></span>

<span data-ttu-id="ac16f-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ac16f-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ac16f-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac16f-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac16f-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac16f-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac16f-643">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac16f-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac16f-644">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-644">`OperationService` operations:</span></span>

<span data-ttu-id="ac16f-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ac16f-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="ac16f-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ac16f-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ac16f-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac16f-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac16f-648">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac16f-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac16f-649">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="ac16f-649">**Second request:**</span></span>

<span data-ttu-id="ac16f-650">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="ac16f-650">Controller operations:</span></span>

<span data-ttu-id="ac16f-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ac16f-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ac16f-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac16f-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac16f-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac16f-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac16f-654">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac16f-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac16f-655">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-655">`OperationService` operations:</span></span>

<span data-ttu-id="ac16f-656">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ac16f-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="ac16f-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ac16f-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ac16f-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ac16f-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ac16f-659">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ac16f-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ac16f-660">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="ac16f-661">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="ac16f-661">*Transient* objects are always different.</span></span> <span data-ttu-id="ac16f-662">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ac16f-663">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="ac16f-664">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="ac16f-665">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ac16f-666">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="ac16f-666">Call services from main</span></span>

<span data-ttu-id="ac16f-667">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ac16f-668">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="ac16f-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="ac16f-669">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ac16f-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ac16f-670">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="ac16f-670">Scope validation</span></span>

<span data-ttu-id="ac16f-671">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac16f-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ac16f-672">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="ac16f-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ac16f-673">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="ac16f-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ac16f-674">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="ac16f-675">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="ac16f-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ac16f-676">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="ac16f-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ac16f-677">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="ac16f-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ac16f-678">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ac16f-679">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="ac16f-680">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="ac16f-680">Request Services</span></span>

<span data-ttu-id="ac16f-681">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="ac16f-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="ac16f-682">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="ac16f-683">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ac16f-684">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="ac16f-685">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="ac16f-686">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="ac16f-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="ac16f-687">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ac16f-688">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="ac16f-688">Design services for dependency injection</span></span>

<span data-ttu-id="ac16f-689">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="ac16f-689">Best practices are to:</span></span>

* <span data-ttu-id="ac16f-690">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="ac16f-691">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="ac16f-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ac16f-692">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="ac16f-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="ac16f-693">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ac16f-694">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="ac16f-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ac16f-695">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="ac16f-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ac16f-696">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ac16f-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ac16f-697">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="ac16f-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="ac16f-698">Tenga en cuenta que las clases del modelo de página de :::no-loc(Razor)::: Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="ac16f-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="ac16f-699">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="ac16f-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ac16f-700">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="ac16f-700">Disposal of services</span></span>

<span data-ttu-id="ac16f-701">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="ac16f-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ac16f-702">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="ac16f-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="ac16f-703">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="ac16f-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="ac16f-704">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ac16f-704">In the following example:</span></span>

* <span data-ttu-id="ac16f-705">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ac16f-706">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="ac16f-707">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="ac16f-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ac16f-708">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ac16f-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ac16f-709">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="ac16f-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ac16f-710">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="ac16f-710">Transient, limited lifetime</span></span>

<span data-ttu-id="ac16f-711">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="ac16f-711">**Scenario**</span></span>

<span data-ttu-id="ac16f-712">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="ac16f-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="ac16f-713">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="ac16f-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="ac16f-714">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="ac16f-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="ac16f-715">**Solución**</span><span class="sxs-lookup"><span data-stu-id="ac16f-715">**Solution**</span></span>

<span data-ttu-id="ac16f-716">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="ac16f-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="ac16f-717">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="ac16f-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="ac16f-718">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="ac16f-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="ac16f-719">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="ac16f-720">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ac16f-721">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="ac16f-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="ac16f-722">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="ac16f-722">**Scenario**</span></span>

<span data-ttu-id="ac16f-723">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="ac16f-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="ac16f-724">**Solución**</span><span class="sxs-lookup"><span data-stu-id="ac16f-724">**Solution**</span></span>

<span data-ttu-id="ac16f-725">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="ac16f-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="ac16f-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="ac16f-727">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ac16f-728">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="ac16f-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="ac16f-729">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="ac16f-729">General Guidelines</span></span>

* <span data-ttu-id="ac16f-730">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="ac16f-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="ac16f-731">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="ac16f-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="ac16f-732">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="ac16f-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="ac16f-733">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="ac16f-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="ac16f-734">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ac16f-735">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="ac16f-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="ac16f-736">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="ac16f-737">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ac16f-738">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="ac16f-738">Default service container replacement</span></span>

<span data-ttu-id="ac16f-739">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="ac16f-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ac16f-740">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="ac16f-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="ac16f-741">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="ac16f-741">Property injection</span></span>
* <span data-ttu-id="ac16f-742">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="ac16f-742">Injection based on name</span></span>
* <span data-ttu-id="ac16f-743">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="ac16f-743">Child containers</span></span>
* <span data-ttu-id="ac16f-744">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="ac16f-744">Custom lifetime management</span></span>
* <span data-ttu-id="ac16f-745">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="ac16f-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ac16f-746">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-746">Convention-based registration</span></span>

<span data-ttu-id="ac16f-747">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ac16f-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="ac16f-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="ac16f-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ac16f-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ac16f-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="ac16f-750">Grace</span><span class="sxs-lookup"><span data-stu-id="ac16f-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="ac16f-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="ac16f-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="ac16f-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="ac16f-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="ac16f-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ac16f-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="ac16f-754">Unity</span><span class="sxs-lookup"><span data-stu-id="ac16f-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="ac16f-755">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="ac16f-755">Thread safety</span></span>

<span data-ttu-id="ac16f-756">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="ac16f-757">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="ac16f-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="ac16f-758">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ac16f-759">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="ac16f-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ac16f-760">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="ac16f-760">Recommendations</span></span>

* <span data-ttu-id="ac16f-761">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="ac16f-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ac16f-762">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="ac16f-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ac16f-763">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ac16f-764">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ac16f-765">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ac16f-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ac16f-766">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="ac16f-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="ac16f-767">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ac16f-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="ac16f-768">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="ac16f-768">Avoid static access to services.</span></span> <span data-ttu-id="ac16f-769">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="ac16f-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="ac16f-770">Evite usar el *patrón de localizador de servicios* , que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="ac16f-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="ac16f-771">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="ac16f-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="ac16f-772">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="ac16f-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="ac16f-773">**Correcto** :</span><span class="sxs-lookup"><span data-stu-id="ac16f-773">**Correct** :</span></span>

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

* <span data-ttu-id="ac16f-774">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="ac16f-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="ac16f-775">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ac16f-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="ac16f-776">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="ac16f-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ac16f-777">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="ac16f-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ac16f-778">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="ac16f-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ac16f-779">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="ac16f-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac16f-780">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ac16f-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ac16f-781">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac16f-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="ac16f-782">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ac16f-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="ac16f-783">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="ac16f-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ac16f-784">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="ac16f-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="ac16f-785">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac16f-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
