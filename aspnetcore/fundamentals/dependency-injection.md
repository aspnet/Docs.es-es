---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: fcfa9e93228cdf71b33e67aeab38fdd9a3295b75
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819222"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f8ea2-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ea2-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="f8ea2-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f8ea2-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8ea2-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f8ea2-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f8ea2-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8ea2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f8ea2-108">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f8ea2-108">Overview of dependency injection</span></span>

<span data-ttu-id="f8ea2-109">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f8ea2-110">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f8ea2-111">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f8ea2-112">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f8ea2-113">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f8ea2-114">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f8ea2-115">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f8ea2-116">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f8ea2-117">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f8ea2-118">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f8ea2-119">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f8ea2-120">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f8ea2-121">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f8ea2-122">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f8ea2-123">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f8ea2-124">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f8ea2-125">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f8ea2-126">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f8ea2-127">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f8ea2-128">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f8ea2-129">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f8ea2-130">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f8ea2-131">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f8ea2-132">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f8ea2-133">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f8ea2-134">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f8ea2-135">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8ea2-136">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f8ea2-137">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f8ea2-138">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f8ea2-139">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f8ea2-140">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f8ea2-141">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f8ea2-142">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f8ea2-143">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f8ea2-144">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f8ea2-145">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f8ea2-146">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f8ea2-147">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f8ea2-148">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f8ea2-149">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="f8ea2-149">Services injected into Startup</span></span>

<span data-ttu-id="f8ea2-150">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f8ea2-151">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f8ea2-152">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f8ea2-153">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="f8ea2-153">Framework-provided services</span></span>

<span data-ttu-id="f8ea2-154">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f8ea2-155">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f8ea2-156">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f8ea2-157">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f8ea2-158">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-158">Service Type</span></span> | <span data-ttu-id="f8ea2-159">Período de duración</span><span class="sxs-lookup"><span data-stu-id="f8ea2-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-160">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="f8ea2-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="f8ea2-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f8ea2-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f8ea2-164">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f8ea2-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-166">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f8ea2-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-170">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f8ea2-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f8ea2-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f8ea2-174">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="f8ea2-174">Register additional services with extension methods</span></span>

<span data-ttu-id="f8ea2-175">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f8ea2-176">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f8ea2-177">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f8ea2-178">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-178">Service lifetimes</span></span>

<span data-ttu-id="f8ea2-179">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f8ea2-180">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f8ea2-181">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-181">Transient</span></span>

<span data-ttu-id="f8ea2-182">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f8ea2-183">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f8ea2-184">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f8ea2-185">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="f8ea2-185">Scoped</span></span>

<span data-ttu-id="f8ea2-186">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f8ea2-187">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f8ea2-188">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f8ea2-189">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f8ea2-190">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f8ea2-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-191">Singleton</span></span>

<span data-ttu-id="f8ea2-192">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f8ea2-193">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f8ea2-194">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f8ea2-195">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f8ea2-196">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f8ea2-197">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f8ea2-198">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f8ea2-199">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-199">Service registration methods</span></span>

<span data-ttu-id="f8ea2-200">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f8ea2-201">Método</span><span class="sxs-lookup"><span data-stu-id="f8ea2-201">Method</span></span> | <span data-ttu-id="f8ea2-202">Automático</span><span class="sxs-lookup"><span data-stu-id="f8ea2-202">Automatic</span></span><br><span data-ttu-id="f8ea2-203">objeto</span><span class="sxs-lookup"><span data-stu-id="f8ea2-203">object</span></span><br><span data-ttu-id="f8ea2-204">eliminación</span><span class="sxs-lookup"><span data-stu-id="f8ea2-204">disposal</span></span> | <span data-ttu-id="f8ea2-205">Múltiple</span><span class="sxs-lookup"><span data-stu-id="f8ea2-205">Multiple</span></span><br><span data-ttu-id="f8ea2-206">implementaciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-206">implementations</span></span> | <span data-ttu-id="f8ea2-207">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="f8ea2-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f8ea2-208">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f8ea2-209">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-209">Yes</span></span> | <span data-ttu-id="f8ea2-210">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-210">Yes</span></span> | <span data-ttu-id="f8ea2-211">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-212">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f8ea2-213">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-213">Yes</span></span> | <span data-ttu-id="f8ea2-214">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-214">Yes</span></span> | <span data-ttu-id="f8ea2-215">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f8ea2-216">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f8ea2-217">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-217">Yes</span></span> | <span data-ttu-id="f8ea2-218">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-218">No</span></span> | <span data-ttu-id="f8ea2-219">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-220">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f8ea2-221">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-221">No</span></span> | <span data-ttu-id="f8ea2-222">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-222">Yes</span></span> | <span data-ttu-id="f8ea2-223">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-224">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f8ea2-225">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-225">No</span></span> | <span data-ttu-id="f8ea2-226">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-226">No</span></span> | <span data-ttu-id="f8ea2-227">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-227">Yes</span></span> |

<span data-ttu-id="f8ea2-228">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f8ea2-229">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f8ea2-230">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f8ea2-231">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f8ea2-232">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f8ea2-233">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f8ea2-234">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f8ea2-235">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f8ea2-236">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f8ea2-237">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f8ea2-238">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f8ea2-239">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f8ea2-240">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f8ea2-241">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="f8ea2-241">Constructor injection behavior</span></span>

<span data-ttu-id="f8ea2-242">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f8ea2-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f8ea2-244">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f8ea2-245">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f8ea2-246">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f8ea2-247">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f8ea2-248">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f8ea2-249">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f8ea2-249">Entity Framework contexts</span></span>

<span data-ttu-id="f8ea2-250">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f8ea2-251">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f8ea2-252">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f8ea2-253">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="f8ea2-253">Lifetime and registration options</span></span>

<span data-ttu-id="f8ea2-254">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f8ea2-255">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f8ea2-256">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f8ea2-257">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f8ea2-258">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f8ea2-259">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f8ea2-260">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f8ea2-261">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f8ea2-262">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f8ea2-263">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f8ea2-264">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f8ea2-265">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f8ea2-266">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f8ea2-267">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f8ea2-268">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f8ea2-269">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f8ea2-270">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f8ea2-271">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f8ea2-272">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f8ea2-273">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-273">**First request:**</span></span>

<span data-ttu-id="f8ea2-274">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-274">Controller operations:</span></span>

<span data-ttu-id="f8ea2-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f8ea2-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f8ea2-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8ea2-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8ea2-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-278">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-279">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-279">`OperationService` operations:</span></span>

<span data-ttu-id="f8ea2-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f8ea2-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f8ea2-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8ea2-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8ea2-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-283">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-284">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-284">**Second request:**</span></span>

<span data-ttu-id="f8ea2-285">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-285">Controller operations:</span></span>

<span data-ttu-id="f8ea2-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f8ea2-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f8ea2-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8ea2-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8ea2-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-289">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-290">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-290">`OperationService` operations:</span></span>

<span data-ttu-id="f8ea2-291">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f8ea2-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f8ea2-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8ea2-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8ea2-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-294">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-295">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f8ea2-296">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-296">*Transient* objects are always different.</span></span> <span data-ttu-id="f8ea2-297">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f8ea2-298">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f8ea2-299">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f8ea2-300">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f8ea2-301">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="f8ea2-301">Call services from main</span></span>

<span data-ttu-id="f8ea2-302">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f8ea2-303">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f8ea2-304">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="f8ea2-305">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="f8ea2-305">Scope validation</span></span>

<span data-ttu-id="f8ea2-306">Cuando la aplicación se ejecuta en el entorno de desarrollo y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f8ea2-307">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f8ea2-308">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f8ea2-309">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f8ea2-310">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f8ea2-311">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f8ea2-312">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f8ea2-313">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f8ea2-314">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f8ea2-315">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="f8ea2-315">Request Services</span></span>

<span data-ttu-id="f8ea2-316">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f8ea2-317">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f8ea2-318">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f8ea2-319">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f8ea2-320">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="f8ea2-321">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-321">This yields classes that are easier to test.</span></span>

<span data-ttu-id="f8ea2-322">ASP.NET Core crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios de ámbito.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-322">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="f8ea2-323">Todos los servicios con ámbito son válidos mientras la solicitud está activa.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-323">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="f8ea2-324">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f8ea2-325">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f8ea2-325">Design services for dependency injection</span></span>

<span data-ttu-id="f8ea2-326">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-326">Best practices are to:</span></span>

* <span data-ttu-id="f8ea2-327">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-327">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f8ea2-328">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f8ea2-329">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-329">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f8ea2-330">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f8ea2-331">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-331">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f8ea2-332">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f8ea2-333">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f8ea2-334">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f8ea2-335">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f8ea2-336">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f8ea2-337">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-337">Disposal of services</span></span>

<span data-ttu-id="f8ea2-338">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f8ea2-339">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f8ea2-340">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f8ea2-341">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-341">In the following example:</span></span>

* <span data-ttu-id="f8ea2-342">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-342">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f8ea2-343">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-343">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f8ea2-344">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-344">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f8ea2-345">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f8ea2-346">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="f8ea2-346">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f8ea2-347">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="f8ea2-347">Transient, limited lifetime</span></span>

<span data-ttu-id="f8ea2-348">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-348">**Scenario**</span></span>

<span data-ttu-id="f8ea2-349">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f8ea2-350">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-350">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f8ea2-351">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-351">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f8ea2-352">**Solución**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-352">**Solution**</span></span>

<span data-ttu-id="f8ea2-353">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-353">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f8ea2-354">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f8ea2-355">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-355">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f8ea2-356">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f8ea2-357">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f8ea2-358">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="f8ea2-358">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f8ea2-359">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-359">**Scenario**</span></span>

<span data-ttu-id="f8ea2-360">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f8ea2-361">**Solución**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-361">**Solution**</span></span>

<span data-ttu-id="f8ea2-362">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f8ea2-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f8ea2-364">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f8ea2-365">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-365">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f8ea2-366">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="f8ea2-366">General Guidelines</span></span>

* <span data-ttu-id="f8ea2-367">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f8ea2-368">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-368">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f8ea2-369">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f8ea2-370">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f8ea2-371">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f8ea2-372">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f8ea2-373">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-373">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f8ea2-374">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f8ea2-375">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="f8ea2-375">Default service container replacement</span></span>

<span data-ttu-id="f8ea2-376">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f8ea2-377">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f8ea2-378">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="f8ea2-378">Property injection</span></span>
* <span data-ttu-id="f8ea2-379">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="f8ea2-379">Injection based on name</span></span>
* <span data-ttu-id="f8ea2-380">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-380">Child containers</span></span>
* <span data-ttu-id="f8ea2-381">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="f8ea2-381">Custom lifetime management</span></span>
* <span data-ttu-id="f8ea2-382">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="f8ea2-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f8ea2-383">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-383">Convention-based registration</span></span>

<span data-ttu-id="f8ea2-384">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-384">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f8ea2-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="f8ea2-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f8ea2-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f8ea2-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8ea2-387">Grace</span><span class="sxs-lookup"><span data-stu-id="f8ea2-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f8ea2-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="f8ea2-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8ea2-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="f8ea2-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f8ea2-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f8ea2-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f8ea2-391">Unity</span><span class="sxs-lookup"><span data-stu-id="f8ea2-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f8ea2-392">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="f8ea2-392">Thread safety</span></span>

<span data-ttu-id="f8ea2-393">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="f8ea2-394">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f8ea2-395">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f8ea2-396">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f8ea2-397">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-397">Recommendations</span></span>

* <span data-ttu-id="f8ea2-398">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f8ea2-399">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f8ea2-400">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-400">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f8ea2-401">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f8ea2-402">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f8ea2-403">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f8ea2-404">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-404">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f8ea2-405">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="f8ea2-406">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-406">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f8ea2-407">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="f8ea2-408">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-408">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="f8ea2-409">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-409">**Correct**:</span></span>

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

* <span data-ttu-id="f8ea2-410">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f8ea2-411">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="f8ea2-412">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f8ea2-413">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f8ea2-414">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f8ea2-415">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-415">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f8ea2-416">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="f8ea2-417">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f8ea2-417">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="f8ea2-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="f8ea2-419">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="f8ea2-420">Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8ea2-421">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f8ea2-421">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f8ea2-422">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ea2-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f8ea2-423">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f8ea2-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="f8ea2-424">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="f8ea2-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f8ea2-425">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="f8ea2-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f8ea2-426">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ea2-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8ea2-427">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f8ea2-428">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f8ea2-429">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8ea2-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f8ea2-430">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f8ea2-430">Overview of dependency injection</span></span>

<span data-ttu-id="f8ea2-431">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-431">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f8ea2-432">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f8ea2-433">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f8ea2-434">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f8ea2-435">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f8ea2-436">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f8ea2-437">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f8ea2-438">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f8ea2-439">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f8ea2-440">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f8ea2-441">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f8ea2-442">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-442">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f8ea2-443">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-443">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f8ea2-444">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-444">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f8ea2-445">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f8ea2-446">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f8ea2-447">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f8ea2-448">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f8ea2-449">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f8ea2-450">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f8ea2-451">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f8ea2-452">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f8ea2-453">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-453">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f8ea2-454">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f8ea2-455">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f8ea2-456">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f8ea2-457">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8ea2-458">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f8ea2-459">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f8ea2-460">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f8ea2-461">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f8ea2-462">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f8ea2-463">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f8ea2-464">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f8ea2-465">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-465">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f8ea2-466">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f8ea2-467">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f8ea2-468">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f8ea2-469">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-469">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f8ea2-470">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f8ea2-471">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="f8ea2-471">Services injected into Startup</span></span>

<span data-ttu-id="f8ea2-472">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="f8ea2-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f8ea2-473">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-473">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f8ea2-474">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-474">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f8ea2-475">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="f8ea2-475">Framework-provided services</span></span>

<span data-ttu-id="f8ea2-476">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f8ea2-477">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f8ea2-478">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f8ea2-479">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-479">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f8ea2-480">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-480">Service Type</span></span> | <span data-ttu-id="f8ea2-481">Período de duración</span><span class="sxs-lookup"><span data-stu-id="f8ea2-481">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-482">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="f8ea2-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="f8ea2-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-484">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f8ea2-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f8ea2-486">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-486">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f8ea2-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-487">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-488">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-488">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f8ea2-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-490">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f8ea2-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-491">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-492">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-492">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f8ea2-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-493">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f8ea2-494">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-494">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f8ea2-495">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-495">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f8ea2-496">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="f8ea2-496">Register additional services with extension methods</span></span>

<span data-ttu-id="f8ea2-497">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f8ea2-498">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f8ea2-499">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f8ea2-500">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-500">Service lifetimes</span></span>

<span data-ttu-id="f8ea2-501">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-501">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f8ea2-502">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f8ea2-503">Transitorio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-503">Transient</span></span>

<span data-ttu-id="f8ea2-504">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f8ea2-505">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-505">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f8ea2-506">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-506">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f8ea2-507">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="f8ea2-507">Scoped</span></span>

<span data-ttu-id="f8ea2-508">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-508">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f8ea2-509">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-509">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f8ea2-510">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-510">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f8ea2-511">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-511">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f8ea2-512">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-512">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f8ea2-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="f8ea2-513">Singleton</span></span>

<span data-ttu-id="f8ea2-514">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-514">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f8ea2-515">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-515">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f8ea2-516">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-516">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f8ea2-517">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-517">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f8ea2-518">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-518">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f8ea2-519">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-519">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f8ea2-520">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-520">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f8ea2-521">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="f8ea2-521">Service registration methods</span></span>

<span data-ttu-id="f8ea2-522">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-522">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f8ea2-523">Método</span><span class="sxs-lookup"><span data-stu-id="f8ea2-523">Method</span></span> | <span data-ttu-id="f8ea2-524">Automático</span><span class="sxs-lookup"><span data-stu-id="f8ea2-524">Automatic</span></span><br><span data-ttu-id="f8ea2-525">objeto</span><span class="sxs-lookup"><span data-stu-id="f8ea2-525">object</span></span><br><span data-ttu-id="f8ea2-526">eliminación</span><span class="sxs-lookup"><span data-stu-id="f8ea2-526">disposal</span></span> | <span data-ttu-id="f8ea2-527">Múltiple</span><span class="sxs-lookup"><span data-stu-id="f8ea2-527">Multiple</span></span><br><span data-ttu-id="f8ea2-528">implementaciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-528">implementations</span></span> | <span data-ttu-id="f8ea2-529">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="f8ea2-529">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f8ea2-530">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-530">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f8ea2-531">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-531">Yes</span></span> | <span data-ttu-id="f8ea2-532">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-532">Yes</span></span> | <span data-ttu-id="f8ea2-533">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-533">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-534">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f8ea2-535">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-535">Yes</span></span> | <span data-ttu-id="f8ea2-536">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-536">Yes</span></span> | <span data-ttu-id="f8ea2-537">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-537">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f8ea2-538">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-538">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f8ea2-539">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-539">Yes</span></span> | <span data-ttu-id="f8ea2-540">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-540">No</span></span> | <span data-ttu-id="f8ea2-541">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-541">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-542">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-542">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f8ea2-543">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-543">No</span></span> | <span data-ttu-id="f8ea2-544">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-544">Yes</span></span> | <span data-ttu-id="f8ea2-545">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-545">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f8ea2-546">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-546">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f8ea2-547">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-547">No</span></span> | <span data-ttu-id="f8ea2-548">No</span><span class="sxs-lookup"><span data-stu-id="f8ea2-548">No</span></span> | <span data-ttu-id="f8ea2-549">Sí</span><span class="sxs-lookup"><span data-stu-id="f8ea2-549">Yes</span></span> |

<span data-ttu-id="f8ea2-550">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-550">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f8ea2-551">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-551">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f8ea2-552">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-552">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f8ea2-553">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-553">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f8ea2-554">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-554">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f8ea2-555">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-555">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f8ea2-556">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-556">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f8ea2-557">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-557">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f8ea2-558">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-558">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f8ea2-559">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-559">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f8ea2-560">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-560">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f8ea2-561">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-561">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f8ea2-562">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-562">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f8ea2-563">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="f8ea2-563">Constructor injection behavior</span></span>

<span data-ttu-id="f8ea2-564">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-564">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f8ea2-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f8ea2-566">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-566">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f8ea2-567">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-567">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f8ea2-568">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-568">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f8ea2-569">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-569">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f8ea2-570">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-570">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f8ea2-571">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f8ea2-571">Entity Framework contexts</span></span>

<span data-ttu-id="f8ea2-572">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-572">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f8ea2-573">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-573">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f8ea2-574">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-574">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f8ea2-575">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="f8ea2-575">Lifetime and registration options</span></span>

<span data-ttu-id="f8ea2-576">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-576">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f8ea2-577">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-577">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f8ea2-578">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-578">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f8ea2-579">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-579">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f8ea2-580">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-580">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f8ea2-581">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-581">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f8ea2-582">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-582">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f8ea2-583">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-583">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f8ea2-584">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-584">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f8ea2-585">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-585">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f8ea2-586">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-586">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f8ea2-587">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-587">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f8ea2-588">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-588">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f8ea2-589">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-589">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f8ea2-590">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-590">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f8ea2-591">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-591">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f8ea2-592">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-592">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f8ea2-593">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-593">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f8ea2-594">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-594">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f8ea2-595">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-595">**First request:**</span></span>

<span data-ttu-id="f8ea2-596">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-596">Controller operations:</span></span>

<span data-ttu-id="f8ea2-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f8ea2-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f8ea2-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8ea2-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8ea2-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-600">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-600">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-601">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-601">`OperationService` operations:</span></span>

<span data-ttu-id="f8ea2-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f8ea2-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f8ea2-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8ea2-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8ea2-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-605">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-605">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-606">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-606">**Second request:**</span></span>

<span data-ttu-id="f8ea2-607">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-607">Controller operations:</span></span>

<span data-ttu-id="f8ea2-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f8ea2-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f8ea2-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8ea2-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8ea2-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-611">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-611">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-612">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-612">`OperationService` operations:</span></span>

<span data-ttu-id="f8ea2-613">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f8ea2-613">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f8ea2-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8ea2-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8ea2-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8ea2-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8ea2-616">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8ea2-616">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8ea2-617">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-617">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f8ea2-618">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-618">*Transient* objects are always different.</span></span> <span data-ttu-id="f8ea2-619">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-619">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f8ea2-620">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-620">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f8ea2-621">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-621">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f8ea2-622">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-622">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f8ea2-623">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="f8ea2-623">Call services from main</span></span>

<span data-ttu-id="f8ea2-624">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-624">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f8ea2-625">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-625">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f8ea2-626">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-626">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f8ea2-627">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="f8ea2-627">Scope validation</span></span>

<span data-ttu-id="f8ea2-628">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-628">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f8ea2-629">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-629">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f8ea2-630">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-630">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f8ea2-631">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-631">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f8ea2-632">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-632">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f8ea2-633">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-633">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f8ea2-634">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-634">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f8ea2-635">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-635">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f8ea2-636">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-636">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f8ea2-637">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="f8ea2-637">Request Services</span></span>

<span data-ttu-id="f8ea2-638">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-638">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f8ea2-639">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-639">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f8ea2-640">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-640">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f8ea2-641">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-641">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f8ea2-642">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-642">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f8ea2-643">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-643">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f8ea2-644">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-644">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f8ea2-645">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f8ea2-645">Design services for dependency injection</span></span>

<span data-ttu-id="f8ea2-646">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-646">Best practices are to:</span></span>

* <span data-ttu-id="f8ea2-647">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-647">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f8ea2-648">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-648">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f8ea2-649">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-649">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f8ea2-650">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-650">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f8ea2-651">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-651">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f8ea2-652">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-652">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f8ea2-653">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-653">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f8ea2-654">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-654">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f8ea2-655">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-655">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f8ea2-656">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-656">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f8ea2-657">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-657">Disposal of services</span></span>

<span data-ttu-id="f8ea2-658">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-658">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f8ea2-659">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-659">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f8ea2-660">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-660">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f8ea2-661">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-661">In the following example:</span></span>

* <span data-ttu-id="f8ea2-662">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-662">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f8ea2-663">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-663">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f8ea2-664">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-664">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f8ea2-665">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-665">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f8ea2-666">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="f8ea2-666">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f8ea2-667">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="f8ea2-667">Transient, limited lifetime</span></span>

<span data-ttu-id="f8ea2-668">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-668">**Scenario**</span></span>

<span data-ttu-id="f8ea2-669">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-669">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f8ea2-670">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-670">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f8ea2-671">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-671">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f8ea2-672">**Solución**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-672">**Solution**</span></span>

<span data-ttu-id="f8ea2-673">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-673">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f8ea2-674">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-674">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f8ea2-675">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-675">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f8ea2-676">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-676">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f8ea2-677">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-677">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f8ea2-678">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="f8ea2-678">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f8ea2-679">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-679">**Scenario**</span></span>

<span data-ttu-id="f8ea2-680">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-680">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f8ea2-681">**Solución**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-681">**Solution**</span></span>

<span data-ttu-id="f8ea2-682">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-682">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f8ea2-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f8ea2-684">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-684">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f8ea2-685">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-685">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f8ea2-686">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="f8ea2-686">General Guidelines</span></span>

* <span data-ttu-id="f8ea2-687">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-687">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f8ea2-688">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-688">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f8ea2-689">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-689">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f8ea2-690">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-690">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f8ea2-691">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-691">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f8ea2-692">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-692">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f8ea2-693">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-693">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f8ea2-694">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-694">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f8ea2-695">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="f8ea2-695">Default service container replacement</span></span>

<span data-ttu-id="f8ea2-696">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-696">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f8ea2-697">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-697">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f8ea2-698">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="f8ea2-698">Property injection</span></span>
* <span data-ttu-id="f8ea2-699">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="f8ea2-699">Injection based on name</span></span>
* <span data-ttu-id="f8ea2-700">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="f8ea2-700">Child containers</span></span>
* <span data-ttu-id="f8ea2-701">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="f8ea2-701">Custom lifetime management</span></span>
* <span data-ttu-id="f8ea2-702">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="f8ea2-702">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f8ea2-703">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-703">Convention-based registration</span></span>

<span data-ttu-id="f8ea2-704">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-704">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f8ea2-705">Autofac</span><span class="sxs-lookup"><span data-stu-id="f8ea2-705">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f8ea2-706">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f8ea2-706">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8ea2-707">Grace</span><span class="sxs-lookup"><span data-stu-id="f8ea2-707">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f8ea2-708">LightInject</span><span class="sxs-lookup"><span data-stu-id="f8ea2-708">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8ea2-709">Lamar</span><span class="sxs-lookup"><span data-stu-id="f8ea2-709">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f8ea2-710">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f8ea2-710">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f8ea2-711">Unity</span><span class="sxs-lookup"><span data-stu-id="f8ea2-711">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f8ea2-712">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="f8ea2-712">Thread safety</span></span>

<span data-ttu-id="f8ea2-713">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-713">Create thread-safe singleton services.</span></span> <span data-ttu-id="f8ea2-714">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-714">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f8ea2-715">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-715">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f8ea2-716">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-716">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f8ea2-717">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="f8ea2-717">Recommendations</span></span>

* <span data-ttu-id="f8ea2-718">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-718">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f8ea2-719">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-719">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f8ea2-720">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-720">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f8ea2-721">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-721">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f8ea2-722">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-722">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f8ea2-723">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-723">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f8ea2-724">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-724">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f8ea2-725">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-725">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="f8ea2-726">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-726">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="f8ea2-727">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-727">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="f8ea2-728">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="f8ea2-728">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="f8ea2-729">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="f8ea2-729">**Correct**:</span></span>

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

  * <span data-ttu-id="f8ea2-730">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-730">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="f8ea2-731">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f8ea2-731">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f8ea2-732">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-732">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f8ea2-733">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-733">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f8ea2-734">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-734">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f8ea2-735">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="f8ea2-735">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8ea2-736">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f8ea2-736">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f8ea2-737">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ea2-737">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f8ea2-738">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f8ea2-738">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="f8ea2-739">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="f8ea2-739">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f8ea2-740">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="f8ea2-740">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f8ea2-741">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ea2-741">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
