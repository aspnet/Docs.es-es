---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3f7cce475b5c7b0fcbb93644b2c39acd637a6f9d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94595485"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="307f6-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="307f6-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="307f6-104">Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="307f6-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="307f6-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="307f6-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="307f6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="307f6-107">Para obtener información sobre el uso de la inserción de dependencias en aplicaciones que no son aplicaciones web, consulte [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="307f6-108">Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="307f6-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="307f6-109">En este tema se proporciona información sobre la inyección de dependencias en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="307f6-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="307f6-110">La documentación principal sobre el uso de la inserción de dependencias se incluye en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="307f6-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="307f6-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="307f6-112">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-112">Overview of dependency injection</span></span>

<span data-ttu-id="307f6-113">Una *dependencia* es un objeto del que depende otro objeto.</span><span class="sxs-lookup"><span data-stu-id="307f6-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="307f6-114">Examine la clase `MyDependency` siguiente con un método `WriteMessage` del que dependen otras clases:</span><span class="sxs-lookup"><span data-stu-id="307f6-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="307f6-115">Una clase puede crear una instancia de la clase `MyDependency` para usar su método `WriteMessage`.</span><span class="sxs-lookup"><span data-stu-id="307f6-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="307f6-116">En el ejemplo siguiente, la clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="307f6-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="307f6-117">La clase crea y depende directamente de la clase `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="307f6-118">Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las razones siguientes:</span><span class="sxs-lookup"><span data-stu-id="307f6-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="307f6-119">Para reemplazar `MyDependency` por una implementación diferente, se debe modificar la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="307f6-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="307f6-120">Si `MyDependency` tiene dependencias, deben configurarse según la clase `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="307f6-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="307f6-121">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="307f6-122">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="307f6-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="307f6-123">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="307f6-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="307f6-124">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="307f6-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="307f6-125">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="307f6-126">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="307f6-127">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="307f6-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="307f6-128">Por lo general, los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="307f6-129">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="307f6-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="307f6-130">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="307f6-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="307f6-131">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define el método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="307f6-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="307f6-132">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="307f6-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="307f6-133">La aplicación de ejemplo registra el servicio `IMyDependency` con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="307f6-134">El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="307f6-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="307f6-135">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="307f6-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="307f6-136">En la aplicación de ejemplo, el servicio `IMyDependency` se solicita y usa para llamar al método `WriteMessage`:</span><span class="sxs-lookup"><span data-stu-id="307f6-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="307f6-137">Mediante el uso del patrón de DI, el controlador:</span><span class="sxs-lookup"><span data-stu-id="307f6-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="307f6-138">No usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency` que implementa.</span><span class="sxs-lookup"><span data-stu-id="307f6-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="307f6-139">Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.</span><span class="sxs-lookup"><span data-stu-id="307f6-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="307f6-140">No crea una instancia de `MyDependency`, la crea el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="307f6-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="307f6-141">La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:</span><span class="sxs-lookup"><span data-stu-id="307f6-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="307f6-142">El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="307f6-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="307f6-143">`MyDependency2` depende de <xref:Microsoft.Extensions.Logging.ILogger%601>, el que solicita en el constructor.</span><span class="sxs-lookup"><span data-stu-id="307f6-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="307f6-144">`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="307f6-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="307f6-145">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="307f6-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="307f6-146">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="307f6-147">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="307f6-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="307f6-148">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="307f6-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="307f6-149">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="307f6-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="307f6-150">En la terminología de la inserción de dependencias, un servicio:</span><span class="sxs-lookup"><span data-stu-id="307f6-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="307f6-151">Por lo general, es un objeto que proporciona un servicio a otros objetos, como el servicio `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="307f6-152">No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.</span><span class="sxs-lookup"><span data-stu-id="307f6-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="307f6-153">El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido.</span><span class="sxs-lookup"><span data-stu-id="307f6-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="307f6-154">Las implementaciones de `IMyDependency` que se muestran en los ejemplos anteriores se escribieron para mostrar la inserción de DI básica, no para implementar el registro.</span><span class="sxs-lookup"><span data-stu-id="307f6-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="307f6-155">La mayoría de las aplicaciones no deberían tener que escribir registradores.</span><span class="sxs-lookup"><span data-stu-id="307f6-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="307f6-156">En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="307f6-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="307f6-157">Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="307f6-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="307f6-158">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="307f6-158">Services injected into Startup</span></span>

<span data-ttu-id="307f6-159">Los servicios se pueden insertar en el constructor `Startup` y en el método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="307f6-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="307f6-160">Solo se pueden insertar los servicios siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="307f6-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="307f6-161">Cualquier servicio registrado con el contenedor de DI se puede insertar en el método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="307f6-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="307f6-162">Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="307f6-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="307f6-163">Registro de grupos de servicios con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="307f6-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="307f6-164">El marco ASP.NET Core usa una convención para registrar un grupo de servicios relacionados.</span><span class="sxs-lookup"><span data-stu-id="307f6-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="307f6-165">La convención es usar un único método de extensión de `Add{GROUP_NAME}` para registrar todos los servicios requeridos por una característica de marco.</span><span class="sxs-lookup"><span data-stu-id="307f6-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="307f6-166">Por ejemplo, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> registra los servicios necesarios para los controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="307f6-166">For example, the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="307f6-167">El código siguiente lo genera la plantilla de Razor Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span><span class="sxs-lookup"><span data-stu-id="307f6-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="307f6-168">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="307f6-168">Service lifetimes</span></span>

<span data-ttu-id="307f6-169">Consulte [Duración del servicio](/dotnet/core/extensions/dependency-injection#service-lifetimes) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="307f6-170">Para usar servicios con ámbito en middleware, use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="307f6-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="307f6-171">Inserte el servicio en el método `Invoke` o `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="307f6-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="307f6-172">El uso de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) genera una excepción en tiempo de ejecución porque obliga al servicio con ámbito a comportarse como un singleton.</span><span class="sxs-lookup"><span data-stu-id="307f6-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="307f6-173">El ejemplo que aparece en la sección [Opciones de registro y duración](#lifetime-and-registration-options) muestra el enfoque `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="307f6-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="307f6-174">Use [middleware basado en Factory](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="307f6-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="307f6-175">El middleware registrado mediante este enfoque se activa por solicitud de cliente (conexión), lo que permite que los servicios con ámbito se inserten en el método `InvokeAsync` del middleware.</span><span class="sxs-lookup"><span data-stu-id="307f6-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="307f6-176">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="307f6-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="307f6-177">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="307f6-177">Service registration methods</span></span>

<span data-ttu-id="307f6-178">Consulte [Métodos de registro del servicio](/dotnet/core/extensions/dependency-injection#service-registration-methods) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="307f6-179">Es habitual usar varias implementaciones al [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="307f6-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="307f6-180">El registro de un servicio con un solo tipo de implementación es equivalente al registro de ese servicio con la misma implementación y el mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="307f6-181">Por eso no se pueden registrar varias implementaciones de un servicio mediante los métodos que no toman un tipo de servicio explícito.</span><span class="sxs-lookup"><span data-stu-id="307f6-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="307f6-182">Estos métodos pueden registrar varias *instancias* de un servicio, pero todos tienen el mismo tipo de *implementación*.</span><span class="sxs-lookup"><span data-stu-id="307f6-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="307f6-183">Cualquiera de los métodos de registro de servicio anteriores se puede usar para registrar varias instancias de servicio del mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="307f6-184">En el ejemplo siguiente se llama a `AddSingleton` dos veces con `IMyDependency` como tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="307f6-185">La segunda llamada a `AddSingleton` invalida la anterior cuando se resuelve como `IMyDependency`, y se agrega a la anterior cuando varios servicios se resuelven mediante `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="307f6-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="307f6-186">Los servicios aparecen en el orden en que se han registrado al resolverse mediante `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="307f6-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="307f6-187">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="307f6-187">Constructor injection behavior</span></span>

<span data-ttu-id="307f6-188">Consulte [Comportamiento de inserción de constructor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="307f6-189">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="307f6-189">Entity Framework contexts</span></span>

<span data-ttu-id="307f6-190">De manera predeterminada, los contextos de Entity Framework se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="307f6-191">Para usar una duración distinta, especifíquela mediante el uso de una sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>.</span><span class="sxs-lookup"><span data-stu-id="307f6-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="307f6-192">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que la del servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="307f6-193">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="307f6-193">Lifetime and registration options</span></span>

<span data-ttu-id="307f6-194">Para mostrar la diferencia entre las duraciones del servicio y sus opciones de registro, considere las interfaces siguientes que representan una tarea como una operación con un identificador, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="307f6-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="307f6-195">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona las mismas instancias del servicio u otras distintas cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="307f6-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="307f6-196">La clase `Operation` siguiente implementa todas las interfaces anteriores.</span><span class="sxs-lookup"><span data-stu-id="307f6-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="307f6-197">El constructor `Operation` genera un GUID y almacena los 4 últimos caracteres en la propiedad `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="307f6-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="307f6-198">El método `Startup.ConfigureServices` crea varios registros de la clase `Operation` de acuerdo con las duraciones mencionadas:</span><span class="sxs-lookup"><span data-stu-id="307f6-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="307f6-199">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="307f6-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="307f6-200">`IndexModel` y el middleware solicitan cada clase del tipo `IOperation` y registran el valor de `OperationId` de cada una:</span><span class="sxs-lookup"><span data-stu-id="307f6-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="307f6-201">De manera similar al `IndexModel`, el middleware resuelve los mismos servicios:</span><span class="sxs-lookup"><span data-stu-id="307f6-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="307f6-202">Los servicios con ámbito se deben resolver en el método `InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="307f6-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="307f6-203">La salida del registrador muestra:</span><span class="sxs-lookup"><span data-stu-id="307f6-203">The logger output shows:</span></span>

* <span data-ttu-id="307f6-204">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="307f6-204">*Transient* objects are always different.</span></span> <span data-ttu-id="307f6-205">El valor `OperationId` transitorio es distinto en el `IndexModel` y en el middleware.</span><span class="sxs-lookup"><span data-stu-id="307f6-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="307f6-206">Los objetos *con ámbito* son iguales para cada solicitud, pero varían entre solicitudes.</span><span class="sxs-lookup"><span data-stu-id="307f6-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="307f6-207">Los objetos *singleton* son los mismos para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="307f6-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="307f6-208">Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="307f6-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="307f6-209">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="307f6-209">Call services from main</span></span>

<span data-ttu-id="307f6-210">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="307f6-211">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="307f6-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="307f6-212">En el ejemplo siguiente se muestra cómo acceder al servicio `IMyDependency` con ámbito y llamar a su método `WriteMessage` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="307f6-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="307f6-213">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="307f6-213">Scope validation</span></span>

<span data-ttu-id="307f6-214">Consulte [Comportamiento de inserción de constructor](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="307f6-215">Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="307f6-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="307f6-216">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="307f6-216">Request Services</span></span>

<span data-ttu-id="307f6-217">Los servicios disponibles en una solicitud de ASP.NET Core se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="307f6-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="307f6-218">Cuando se solicitan servicios dentro de una solicitud, los servicios y sus dependencias se resuelven desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="307f6-219">El marco crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="307f6-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="307f6-220">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="307f6-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="307f6-221">Se recomienda que solicite las dependencias como parámetros del constructor para resolver los servicios desde la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="307f6-222">Esto genera clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="307f6-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="307f6-223">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-223">Design services for dependency injection</span></span>

<span data-ttu-id="307f6-224">Al diseñar servicios para la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="307f6-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="307f6-225">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="307f6-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="307f6-226">Evitar crear un estado global mediante el diseño de aplicaciones para usar servicios singleton en su lugar.</span><span class="sxs-lookup"><span data-stu-id="307f6-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="307f6-227">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="307f6-228">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="307f6-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="307f6-229">Cree servicios pequeños, bien factorizados y probados con facilidad.</span><span class="sxs-lookup"><span data-stu-id="307f6-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="307f6-230">Si una clase tiene muchas dependencias insertadas, podría ser un signo de que la clase tiene demasiadas responsabilidades e infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="307f6-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="307f6-231">Trate de mover algunas de las responsabilidades de la clase a clases nuevas para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="307f6-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="307f6-232">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="307f6-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="307f6-233">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="307f6-233">Disposal of services</span></span>

<span data-ttu-id="307f6-234">El contenedor llama a <xref:System.IDisposable.Dispose%2A> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="307f6-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="307f6-235">El desarrollador nunca debe eliminar los servicios resueltos desde el contenedor.</span><span class="sxs-lookup"><span data-stu-id="307f6-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="307f6-236">Si un tipo o fábrica se registra como singleton, el contenedor elimina el singleton de manera automática.</span><span class="sxs-lookup"><span data-stu-id="307f6-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="307f6-237">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="307f6-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="307f6-238">La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:</span><span class="sxs-lookup"><span data-stu-id="307f6-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="307f6-239">Servicios no creados por el contenedor de servicios</span><span class="sxs-lookup"><span data-stu-id="307f6-239">Services not created by the service container</span></span>

<span data-ttu-id="307f6-240">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="307f6-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="307f6-241">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="307f6-241">In the preceding code:</span></span>

* <span data-ttu-id="307f6-242">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="307f6-243">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="307f6-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="307f6-244">El desarrollador es responsable de la eliminación de los servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="307f6-245">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="307f6-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="307f6-246">Consulte [Instrucciones de IDisposable para instancias transitorias y compartidas](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) en [la Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="307f6-247">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="307f6-247">Default service container replacement</span></span>

<span data-ttu-id="307f6-248">Consulte [Reemplazo de contenedores de servicio predeterminados](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="307f6-249">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="307f6-249">Recommendations</span></span>

<span data-ttu-id="307f6-250">Consulte [Recomendaciones](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) en [Inserción de dependencias en .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="307f6-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="307f6-251">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="307f6-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="307f6-252">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService%2A> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="307f6-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="307f6-253">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="307f6-253">**Incorrect:**</span></span>

    ![Código incorrecto](dependency-injection/_static/bad.png)

  <span data-ttu-id="307f6-255">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="307f6-255">**Correct**:</span></span>

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
* <span data-ttu-id="307f6-256">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="307f6-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="307f6-257">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="307f6-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="307f6-258">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="307f6-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="307f6-259">Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="307f6-260">La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="307f6-261">Por ejemplo, considere el caso donde `LoginPath` se carga desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="307f6-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="307f6-262">Evite el enfoque siguiente:</span><span class="sxs-lookup"><span data-stu-id="307f6-262">Avoid the following approach:</span></span>

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="307f6-264">En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:</span><span class="sxs-lookup"><span data-stu-id="307f6-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="307f6-265">ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton.</span><span class="sxs-lookup"><span data-stu-id="307f6-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="307f6-266">Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".</span><span class="sxs-lookup"><span data-stu-id="307f6-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="307f6-267">La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores.</span><span class="sxs-lookup"><span data-stu-id="307f6-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="307f6-268">Una manera correcta de obtener `LoginPath` es usar la compatibilidad integrada del patrón de opciones para DI:</span><span class="sxs-lookup"><span data-stu-id="307f6-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="307f6-269">El contenedor captura a los servicios transitorios descartables para su eliminación.</span><span class="sxs-lookup"><span data-stu-id="307f6-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="307f6-270">Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="307f6-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="307f6-271">Habilite la validación con ámbito para asegurarse de que la aplicación no tenga singletons que capturen los servicios con ámbito.</span><span class="sxs-lookup"><span data-stu-id="307f6-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="307f6-272">Para más información, vea [Validación del ámbito](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="307f6-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="307f6-273">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="307f6-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="307f6-274">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="307f6-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="307f6-275">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="307f6-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="307f6-276">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="307f6-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="307f6-277">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="307f6-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) es un marco de trabajo de la aplicación para compilar aplicaciones modulares y multiinquilino en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="307f6-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="307f6-279">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="307f6-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="307f6-280">Consulte los [ejemplos de Orchard Core](https://github.com/OrchardCMS/OrchardCore.Samples) para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="307f6-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="307f6-281">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="307f6-281">Framework-provided services</span></span>

<span data-ttu-id="307f6-282">El método `Startup.ConfigureServices` registra los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="307f6-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="307f6-283">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="307f6-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="307f6-284">En el caso de las aplicaciones basadas en las plantillas de ASP.NET Core, el marco de trabajo registra más de 250 servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="307f6-285">En la tabla siguiente se ilustra una pequeña muestra de estos servicios registrados por el marco:</span><span class="sxs-lookup"><span data-stu-id="307f6-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="307f6-286">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="307f6-286">Service Type</span></span>                                                                                    | <span data-ttu-id="307f6-287">Período de duración</span><span class="sxs-lookup"><span data-stu-id="307f6-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="307f6-288">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="307f6-289">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="307f6-290">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="307f6-291">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="307f6-292">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="307f6-293">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="307f6-294">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="307f6-295">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="307f6-296">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="307f6-297">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="307f6-298">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="307f6-299">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="307f6-300">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="307f6-301">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="307f6-302">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="307f6-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="307f6-303">Patrones de la Conferencia NDC para el desarrollo de aplicaciones de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="307f6-304">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="307f6-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="307f6-305">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="307f6-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="307f6-306">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="307f6-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="307f6-307">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="307f6-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="307f6-308">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="307f6-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="307f6-309">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="307f6-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="307f6-310">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="307f6-311">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="307f6-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="307f6-312">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="307f6-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="307f6-313">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-313">Overview of dependency injection</span></span>

<span data-ttu-id="307f6-314">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="307f6-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="307f6-315">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="307f6-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="307f6-316">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="307f6-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="307f6-317">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="307f6-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="307f6-318">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="307f6-319">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="307f6-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="307f6-320">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="307f6-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="307f6-321">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="307f6-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="307f6-322">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="307f6-323">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="307f6-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="307f6-324">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="307f6-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="307f6-325">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="307f6-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="307f6-326">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="307f6-327">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="307f6-328">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="307f6-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="307f6-329">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="307f6-330">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="307f6-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="307f6-331">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="307f6-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="307f6-332">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="307f6-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="307f6-333">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="307f6-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="307f6-334">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="307f6-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="307f6-335">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="307f6-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="307f6-336">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="307f6-337">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="307f6-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="307f6-338">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="307f6-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="307f6-339">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="307f6-340">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="307f6-341">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="307f6-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="307f6-342">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="307f6-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="307f6-343">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="307f6-344">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="307f6-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="307f6-345">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="307f6-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="307f6-346">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios y potencialmente los configura.</span><span class="sxs-lookup"><span data-stu-id="307f6-346">Each `services.Add{SERVICE_NAME}` extension method adds, and potentially configures, services.</span></span> <span data-ttu-id="307f6-347">Por ejemplo, `services.AddControllersWithViews`, `services.AddRazorPages` y `services.AddControllers` agregan los servicios que requieren las aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="307f6-347">For example, `services.AddControllersWithViews`, `services.AddRazorPages`, and `services.AddControllers` adds the services ASP.NET Core apps require.</span></span> <span data-ttu-id="307f6-348">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="307f6-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="307f6-349">Coloque los métodos de extensión en el espacio de nombres <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-349">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span> <span data-ttu-id="307f6-350">La inclusión de la parte del espacio de nombres `Microsoft.Extensions.DependencyInjection` para los métodos de extensión DI también:</span><span class="sxs-lookup"><span data-stu-id="307f6-350">Including the namespace portion `Microsoft.Extensions.DependencyInjection` for DI extension methods also:</span></span>
>
> * <span data-ttu-id="307f6-351">Permite que se muestren en [IntelliSense](/visualstudio/ide/using-intellisense) sin agregar bloques `using` adicionales.</span><span class="sxs-lookup"><span data-stu-id="307f6-351">Allows them to be displayed in [IntelliSense](/visualstudio/ide/using-intellisense) without adding additional `using` blocks.</span></span>
> * <span data-ttu-id="307f6-352">Evita demasiadas instrucciones `using` en la clase `Startup`, desde donde se llama normalmente a estos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="307f6-352">Prevents excessive `using` statements in the `Startup` class where these extension methods are typically called from.</span></span>

<span data-ttu-id="307f6-353">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="307f6-353">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="307f6-354">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="307f6-354">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="307f6-355">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="307f6-355">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="307f6-356">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="307f6-356">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="307f6-357">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="307f6-357">Services injected into Startup</span></span>

<span data-ttu-id="307f6-358">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="307f6-358">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="307f6-359">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="307f6-359">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="307f6-360">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="307f6-360">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="307f6-361">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="307f6-361">Framework-provided services</span></span>

<span data-ttu-id="307f6-362">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="307f6-362">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="307f6-363">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="307f6-363">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="307f6-364">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="307f6-364">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="307f6-365">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="307f6-365">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="307f6-366">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="307f6-366">Service Type</span></span> | <span data-ttu-id="307f6-367">Período de duración</span><span class="sxs-lookup"><span data-stu-id="307f6-367">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="307f6-368">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-368">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="307f6-369">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-369">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="307f6-370">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="307f6-371">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-371">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="307f6-372">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-372">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="307f6-373">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-373">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="307f6-374">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-374">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="307f6-375">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-375">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="307f6-376">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-376">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="307f6-377">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-377">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="307f6-378">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-378">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="307f6-379">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-379">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="307f6-380">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-380">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="307f6-381">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-381">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="307f6-382">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="307f6-382">Register additional services with extension methods</span></span>

<span data-ttu-id="307f6-383">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-383">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="307f6-384">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="307f6-384">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="307f6-385">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="307f6-385">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="307f6-386">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="307f6-386">Service lifetimes</span></span>

<span data-ttu-id="307f6-387">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="307f6-387">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="307f6-388">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="307f6-388">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="307f6-389">Transitorio</span><span class="sxs-lookup"><span data-stu-id="307f6-389">Transient</span></span>

<span data-ttu-id="307f6-390">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="307f6-390">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="307f6-391">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="307f6-391">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="307f6-392">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="307f6-392">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="307f6-393">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="307f6-393">Scoped</span></span>

<span data-ttu-id="307f6-394">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="307f6-394">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="307f6-395">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="307f6-395">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="307f6-396">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="307f6-396">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="307f6-397">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="307f6-397">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="307f6-398">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="307f6-398">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="307f6-399">Singleton</span><span class="sxs-lookup"><span data-stu-id="307f6-399">Singleton</span></span>

<span data-ttu-id="307f6-400">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-400">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="307f6-401">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="307f6-401">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="307f6-402">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-402">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="307f6-403">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="307f6-403">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="307f6-404">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-404">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="307f6-405">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="307f6-405">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="307f6-406">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="307f6-406">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="307f6-407">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="307f6-407">Service registration methods</span></span>

<span data-ttu-id="307f6-408">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="307f6-408">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="307f6-409">Método</span><span class="sxs-lookup"><span data-stu-id="307f6-409">Method</span></span> | <span data-ttu-id="307f6-410">Automático</span><span class="sxs-lookup"><span data-stu-id="307f6-410">Automatic</span></span><br><span data-ttu-id="307f6-411">objeto</span><span class="sxs-lookup"><span data-stu-id="307f6-411">object</span></span><br><span data-ttu-id="307f6-412">eliminación</span><span class="sxs-lookup"><span data-stu-id="307f6-412">disposal</span></span> | <span data-ttu-id="307f6-413">Múltiple</span><span class="sxs-lookup"><span data-stu-id="307f6-413">Multiple</span></span><br><span data-ttu-id="307f6-414">implementaciones</span><span class="sxs-lookup"><span data-stu-id="307f6-414">implementations</span></span> | <span data-ttu-id="307f6-415">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="307f6-415">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="307f6-416">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="307f6-416">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="307f6-417">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-417">Yes</span></span> | <span data-ttu-id="307f6-418">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-418">Yes</span></span> | <span data-ttu-id="307f6-419">No</span><span class="sxs-lookup"><span data-stu-id="307f6-419">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="307f6-420">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="307f6-420">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="307f6-421">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-421">Yes</span></span> | <span data-ttu-id="307f6-422">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-422">Yes</span></span> | <span data-ttu-id="307f6-423">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-423">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="307f6-424">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="307f6-424">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="307f6-425">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-425">Yes</span></span> | <span data-ttu-id="307f6-426">No</span><span class="sxs-lookup"><span data-stu-id="307f6-426">No</span></span> | <span data-ttu-id="307f6-427">No</span><span class="sxs-lookup"><span data-stu-id="307f6-427">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="307f6-428">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="307f6-428">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="307f6-429">No</span><span class="sxs-lookup"><span data-stu-id="307f6-429">No</span></span> | <span data-ttu-id="307f6-430">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-430">Yes</span></span> | <span data-ttu-id="307f6-431">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-431">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="307f6-432">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="307f6-432">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="307f6-433">No</span><span class="sxs-lookup"><span data-stu-id="307f6-433">No</span></span> | <span data-ttu-id="307f6-434">No</span><span class="sxs-lookup"><span data-stu-id="307f6-434">No</span></span> | <span data-ttu-id="307f6-435">Sí</span><span class="sxs-lookup"><span data-stu-id="307f6-435">Yes</span></span> |

<span data-ttu-id="307f6-436">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="307f6-436">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="307f6-437">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="307f6-437">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="307f6-438">El registro de un servicio con un solo tipo de implementación es equivalente al registro de ese servicio con la misma implementación y el mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-438">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="307f6-439">Por eso no se pueden registrar varias implementaciones de un servicio mediante los métodos que no toman un tipo de servicio explícito.</span><span class="sxs-lookup"><span data-stu-id="307f6-439">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="307f6-440">Estos métodos pueden registrar varias *instancias* de un servicio, pero todos tienen el mismo tipo de *implementación*.</span><span class="sxs-lookup"><span data-stu-id="307f6-440">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="307f6-441">Cualquiera de los métodos de registro de servicio anteriores se puede usar para registrar varias instancias de servicio del mismo tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-441">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="307f6-442">En el ejemplo siguiente se llama a `AddSingleton` dos veces con `IMyDependency` como tipo de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-442">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="307f6-443">La segunda llamada a `AddSingleton` invalida la anterior cuando se resuelve como `IMyDependency`, y se agrega a la anterior cuando varios servicios se resuelven mediante `IEnumerable<IMyDependency>`.</span><span class="sxs-lookup"><span data-stu-id="307f6-443">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="307f6-444">Los servicios aparecen en el orden en que se han registrado al resolverse mediante `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="307f6-444">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="307f6-445">El marco también proporciona métodos de extensión `TryAdd{LIFETIME}`, que registran el servicio solo si todavía no hay registrada una implementación.</span><span class="sxs-lookup"><span data-stu-id="307f6-445">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="307f6-446">En el ejemplo siguiente, la llamada a `AddSingleton` registra `MyDependency` como una implementación para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="307f6-446">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="307f6-447">La llamada a `TryAddSingleton` no tiene ningún efecto, puesto que `IMyDependency` ya tiene una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="307f6-447">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="307f6-448">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="307f6-448">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="307f6-449">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="307f6-449">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="307f6-450">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-450">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="307f6-451">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="307f6-451">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="307f6-452">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="307f6-452">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="307f6-453">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="307f6-453">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="307f6-454">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="307f6-454">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="307f6-455">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="307f6-455">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="307f6-456">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="307f6-456">Constructor injection behavior</span></span>

<span data-ttu-id="307f6-457">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="307f6-457">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="307f6-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="307f6-459">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="307f6-459">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="307f6-460">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="307f6-460">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="307f6-461">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="307f6-461">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="307f6-462">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="307f6-462">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="307f6-463">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-463">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="307f6-464">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="307f6-464">Entity Framework contexts</span></span>

<span data-ttu-id="307f6-465">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-465">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="307f6-466">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="307f6-466">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="307f6-467">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-467">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="307f6-468">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="307f6-468">Lifetime and registration options</span></span>

<span data-ttu-id="307f6-469">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="307f6-469">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="307f6-470">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="307f6-470">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="307f6-471">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="307f6-471">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="307f6-472">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="307f6-472">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="307f6-473">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="307f6-473">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="307f6-474">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="307f6-474">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="307f6-475">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="307f6-475">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="307f6-476">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="307f6-476">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="307f6-477">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="307f6-477">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="307f6-478">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-478">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="307f6-479">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="307f6-479">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="307f6-480">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-480">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="307f6-481">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="307f6-481">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="307f6-482">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="307f6-482">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="307f6-483">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="307f6-483">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="307f6-484">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="307f6-484">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="307f6-485">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="307f6-485">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="307f6-486">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="307f6-486">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="307f6-487">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="307f6-487">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="307f6-488">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="307f6-488">**First request:**</span></span>

<span data-ttu-id="307f6-489">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="307f6-489">Controller operations:</span></span>

<span data-ttu-id="307f6-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="307f6-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="307f6-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="307f6-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="307f6-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="307f6-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="307f6-493">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="307f6-493">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="307f6-494">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="307f6-494">`OperationService` operations:</span></span>

<span data-ttu-id="307f6-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="307f6-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="307f6-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="307f6-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="307f6-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="307f6-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="307f6-498">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="307f6-498">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="307f6-499">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="307f6-499">**Second request:**</span></span>

<span data-ttu-id="307f6-500">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="307f6-500">Controller operations:</span></span>

<span data-ttu-id="307f6-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="307f6-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="307f6-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="307f6-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="307f6-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="307f6-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="307f6-504">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="307f6-504">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="307f6-505">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="307f6-505">`OperationService` operations:</span></span>

<span data-ttu-id="307f6-506">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="307f6-506">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="307f6-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="307f6-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="307f6-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="307f6-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="307f6-509">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="307f6-509">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="307f6-510">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="307f6-510">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="307f6-511">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="307f6-511">*Transient* objects are always different.</span></span> <span data-ttu-id="307f6-512">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-512">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="307f6-513">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-513">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="307f6-514">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="307f6-514">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="307f6-515">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-515">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="307f6-516">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="307f6-516">Call services from main</span></span>

<span data-ttu-id="307f6-517">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-517">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="307f6-518">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="307f6-518">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="307f6-519">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="307f6-519">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="307f6-520">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="307f6-520">Scope validation</span></span>

<span data-ttu-id="307f6-521">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="307f6-521">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="307f6-522">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="307f6-522">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="307f6-523">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="307f6-523">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="307f6-524">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="307f6-524">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="307f6-525">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="307f6-525">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="307f6-526">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="307f6-526">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="307f6-527">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="307f6-527">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="307f6-528">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="307f6-528">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="307f6-529">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="307f6-529">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="307f6-530">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="307f6-530">Request Services</span></span>

<span data-ttu-id="307f6-531">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="307f6-531">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="307f6-532">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-532">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="307f6-533">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-533">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="307f6-534">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="307f6-534">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="307f6-535">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-535">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="307f6-536">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="307f6-536">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="307f6-537">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="307f6-537">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="307f6-538">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="307f6-538">Design services for dependency injection</span></span>

<span data-ttu-id="307f6-539">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="307f6-539">Best practices are to:</span></span>

* <span data-ttu-id="307f6-540">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-540">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="307f6-541">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="307f6-541">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="307f6-542">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="307f6-542">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="307f6-543">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-543">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="307f6-544">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="307f6-544">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="307f6-545">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="307f6-545">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="307f6-546">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="307f6-546">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="307f6-547">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="307f6-547">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="307f6-548">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="307f6-548">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="307f6-549">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="307f6-549">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="307f6-550">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="307f6-550">Disposal of services</span></span>

<span data-ttu-id="307f6-551">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="307f6-551">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="307f6-552">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="307f6-552">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="307f6-553">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="307f6-553">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="307f6-554">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="307f6-554">In the following example:</span></span>

* <span data-ttu-id="307f6-555">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-555">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="307f6-556">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="307f6-556">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="307f6-557">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="307f6-557">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="307f6-558">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="307f6-558">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="307f6-559">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="307f6-559">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="307f6-560">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="307f6-560">Transient, limited lifetime</span></span>

<span data-ttu-id="307f6-561">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="307f6-561">**Scenario**</span></span>

<span data-ttu-id="307f6-562">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="307f6-562">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="307f6-563">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="307f6-563">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="307f6-564">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="307f6-564">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="307f6-565">**Solución**</span><span class="sxs-lookup"><span data-stu-id="307f6-565">**Solution**</span></span>

<span data-ttu-id="307f6-566">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="307f6-566">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="307f6-567">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="307f6-567">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="307f6-568">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="307f6-568">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="307f6-569">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="307f6-569">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="307f6-570">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-570">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="307f6-571">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="307f6-571">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="307f6-572">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="307f6-572">**Scenario**</span></span>

<span data-ttu-id="307f6-573">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="307f6-573">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="307f6-574">**Solución**</span><span class="sxs-lookup"><span data-stu-id="307f6-574">**Solution**</span></span>

<span data-ttu-id="307f6-575">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="307f6-575">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="307f6-576">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="307f6-576">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="307f6-577">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="307f6-577">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="307f6-578">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="307f6-578">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="307f6-579">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="307f6-579">General Guidelines</span></span>

* <span data-ttu-id="307f6-580">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="307f6-580">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="307f6-581">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="307f6-581">Use the factory pattern instead.</span></span>
* <span data-ttu-id="307f6-582">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="307f6-582">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="307f6-583">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="307f6-583">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="307f6-584">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="307f6-584">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="307f6-585">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="307f6-585">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="307f6-586">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-586">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="307f6-587">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="307f6-587">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="307f6-588">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="307f6-588">Default service container replacement</span></span>

<span data-ttu-id="307f6-589">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="307f6-589">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="307f6-590">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="307f6-590">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="307f6-591">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="307f6-591">Property injection</span></span>
* <span data-ttu-id="307f6-592">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="307f6-592">Injection based on name</span></span>
* <span data-ttu-id="307f6-593">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="307f6-593">Child containers</span></span>
* <span data-ttu-id="307f6-594">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="307f6-594">Custom lifetime management</span></span>
* <span data-ttu-id="307f6-595">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="307f6-595">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="307f6-596">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="307f6-596">Convention-based registration</span></span>

<span data-ttu-id="307f6-597">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="307f6-597">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="307f6-598">Autofac</span><span class="sxs-lookup"><span data-stu-id="307f6-598">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="307f6-599">DryIoc</span><span class="sxs-lookup"><span data-stu-id="307f6-599">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="307f6-600">Grace</span><span class="sxs-lookup"><span data-stu-id="307f6-600">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="307f6-601">LightInject</span><span class="sxs-lookup"><span data-stu-id="307f6-601">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="307f6-602">Lamar</span><span class="sxs-lookup"><span data-stu-id="307f6-602">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="307f6-603">Stashbox</span><span class="sxs-lookup"><span data-stu-id="307f6-603">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="307f6-604">Unity</span><span class="sxs-lookup"><span data-stu-id="307f6-604">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="307f6-605">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="307f6-605">Thread safety</span></span>

<span data-ttu-id="307f6-606">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="307f6-606">Create thread-safe singleton services.</span></span> <span data-ttu-id="307f6-607">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="307f6-607">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="307f6-608">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="307f6-608">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="307f6-609">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="307f6-609">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="307f6-610">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="307f6-610">Recommendations</span></span>

* <span data-ttu-id="307f6-611">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="307f6-611">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="307f6-612">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="307f6-612">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="307f6-613">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-613">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="307f6-614">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-614">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="307f6-615">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="307f6-615">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="307f6-616">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="307f6-616">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="307f6-617">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="307f6-617">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="307f6-618">Evite el acceso estático a los servicios.</span><span class="sxs-lookup"><span data-stu-id="307f6-618">Avoid static access to services.</span></span> <span data-ttu-id="307f6-619">Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="307f6-619">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="307f6-620">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="307f6-620">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="307f6-621">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="307f6-621">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="307f6-622">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="307f6-622">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="307f6-623">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="307f6-623">**Correct**:</span></span>

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

* <span data-ttu-id="307f6-624">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="307f6-624">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="307f6-625">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="307f6-625">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="307f6-626">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="307f6-626">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="307f6-627">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="307f6-627">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="307f6-628">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="307f6-628">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="307f6-629">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="307f6-629">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="307f6-630">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="307f6-630">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="307f6-631">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="307f6-631">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="307f6-632">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="307f6-632">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="307f6-633">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="307f6-633">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="307f6-634">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="307f6-634">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="307f6-635">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="307f6-635">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
