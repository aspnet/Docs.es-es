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
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628799"
---
# <a name="dependency-injection-in-aspnet-core"></a>Inserción de dependencias en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.

Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.

Para obtener más información sobre la inserción de dependencias de las opciones, vea <xref:fundamentals/configuration/options>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Información general sobre la inserción de dependencias

Una *dependencia* es cualquier objeto requerido por otro objeto. Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:

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

Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase. La clase `MyDependency` es una dependencia de la clase `IndexModel`:

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

La clase crea y depende directamente de la instancia `MyDependency`. Las dependencias de código, como en el ejemplo anterior, son problemáticas y deben evitarse por las siguientes razones:

* Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.
* Si `MyDependency` tiene dependencias, deben configurarse según la clase. En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.
* Esta implementación es difícil para realizar pruebas unitarias. La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.

La inserción de dependencias aborda estos problemas mediante:

* Uso de una interfaz o clase base para abstraer la implementación de dependencias.
* Registro de la dependencia en un contenedor de servicios. ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>. Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.
* *Inserción* del servicio en el constructor de la clase en la que se usa. El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.

En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`. El método <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registra el servicio mediante una duración con ámbito, definida como la duración de una única solicitud. Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Con el patrón de DI:

* El controlador no usa el tipo concreto `MyDependency`, solo la interfaz `IMyDependency`. Esto facilita el cambio de la implementación que el controlador utiliza sin modificar el controlador.
* El controlador no crea una instancia de `MyDependency`, la crea el contenedor de DI.

La implementación de la interfaz de `IMyDependency` se puede mejorar mediante el uso de la API de registro integrada:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

El método `ConfigureServices` actualizado registra la nueva implementación de `IMyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en el constructor. No es raro usar la inserción de dependencias de forma encadenada. Cada dependencia solicitada a su vez solicita sus propias dependencias. El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto. El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.

`ILogger<TCategoryName>` es un [servicio proporcionado por el marco de trabajo](#framework-provided-services).

El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

En la terminología de la inserción de dependencias, un servicio:

* Normalmente es un objeto que proporciona un servicio a otro código de la aplicación, como el servicio `IMyDependency`.
* No está relacionado con un servicio web, aunque el servicio puede utilizar un servicio web.

El marco de trabajo proporciona un sistema de [registro](xref:fundamentals/logging/index) sólido. Las implementaciones de `IMyDependency` se escribieron para mostrar la inserción de DI básica, no para implementar el registro. La mayoría de las aplicaciones no deberían tener que escribir registradores. En el código siguiente se muestra cómo usar el registro predeterminado, que no requiere que los servicios se registren en `ConfigureServices`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Con el código anterior, no es necesario actualizar `ConfigureServices` porque el [registro](xref:fundamentals/logging/index) se proporciona a través del marco de trabajo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a>Servicios insertados en Startup

Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Los servicios se pueden insertar en `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Para obtener más información, vea <xref:fundamentals/startup> y [Acceso a la configuración en Inicio](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-additional-services-with-extension-methods"></a>Registro de servicios adicionales con métodos de extensión

Cuando un método de extensión de colección de servicios está disponible para registrar un servicio:

* La convención es usar un único método de extensión de `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por ese servicio.
* Los servicios dependientes también se registran.

El código siguiente lo genera la plantilla de Razor Pages con cuentas de usuario individuales y muestra cómo agregar servicios adicionales al contenedor mediante los métodos de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

Para obtener más información, vea <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> y <xref:security/authentication/identity>.

Vea la sección [Combinación de colecciones de servicios](#csc) para obtener instrucciones sobre cómo escribir un método de extensión para registrar servicios.

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Duraciones de servicios

Elija una duración adecuada para cada servicio registrado. Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:

### <a name="transient"></a>Transitorio

Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita. Esta duración funciona mejor para servicios sin estado ligeros.

En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.

### <a name="scoped"></a>Con ámbito

Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).

Cuando se usa Entity Framework Core, el método de extensión <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> registra tipos de `DbContext` con una duración de ámbito de forma predeterminada.

En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.

Use servicios con ámbito en middleware con uno de los siguientes métodos:

* Inserte el servicio en el método `Invoke` o `InvokeAsync`. Insertar a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection) produce una excepción en tiempo de ejecución porque hace que el servicio se comporte como un singleton. El ejemplo de las [opciones de duración y de registro](#lifetime-and-registration-options) usa el enfoque `InvokeAsync`.
* [Middleware basado en Factory](<xref:fundamentals/middleware/extensibility>). Los métodos de extensión <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> comprueban si un tipo registrado de middleware implementa <xref:Microsoft.AspNetCore.Http.IMiddleware>. Si es así, la instancia <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> registrada en el contenedor se usa para resolver la implementación <xref:Microsoft.AspNetCore.Http.IMiddleware> en lugar de usar la lógica de activación de middleware basado en convenciones. El middleware se registra como un servicio con ámbito o transitorio en el contenedor de servicios de la aplicación.

Para más información, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

***Nunca*** resuelva un servicio con ámbito desde un singleton. Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores. Basta con:

* Resolver un servicio singleton desde un servicio con ámbito o transitorio.
* Resolver un servicio con ámbito desde otro servicio con ámbito o transitorio.

Por defecto, en el entorno de desarrollo, resolver un servicio desde otro servicio con una duración más larga producirá una excepción. Para más información, vea [Validación del ámbito](#sv).

### <a name="singleton"></a>Singleton

Los servicios de duración de singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean de alguna de las siguientes formas:

* La primera vez que se solicitan.
* Mediante el desarrollador, al proporcionar una instancia de implementación directamente al contenedor. Este enfoque rara vez es necesario.

Cada solicitud posterior usa la misma instancia. Si la aplicación requiere un comportamiento de singleton, permita que el contenedor de servicios administre la duración del servicio. No implemente el modelo de diseño singleton y proporcione el código para desechar el singleton. Los servicios nunca deben desecharse mediante el código que haya resuelto el servicio de un contenedor. Si un tipo o generador se registra como singleton, el contenedor eliminará el singleton automáticamente.

Los servicios singleton deben ser seguros para los subprocesos y se suelen usar en servicios sin estado.

En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación. Dado que no se libera memoria hasta que se apaga la aplicación, se debe tener en cuenta el uso de memoria con un singleton.

> [!WARNING]
> ***Nunca*** resuelva un servicio con ámbito desde un singleton. Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores. Basta con resolver un servicio singleton desde un servicio con ámbito o transitorio.

## <a name="service-registration-methods"></a>Métodos de registro del servicio

Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| Método | Automático<br>objeto<br>eliminación | Múltiple<br>implementaciones | Transferencia de argumentos |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddSingleton<IMyDep, MyDep>();` | Sí | Sí | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | Sí | Sí | Sí |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddSingleton<MyDep>();` | Sí | No | No |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | No | Sí | Sí |
| `AddSingleton(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | No | No | Sí |

Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services). Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).

Los métodos `TryAdd{LIFETIME}` registran el servicio si no hay ya una implementación registrada.

En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`. La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Para obtener más información, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran el servicio si no hay ya una implementación *del mismo tipo*. A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios. Al registrar los servicios, el desarrollador debería agregar una instancia si no se ha agregado ya una del mismo tipo. Por lo general, los autores de bibliotecas usan `TryAddEnumerable` para evitar el registro de varias copias de una implementación en el contenedor.

En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`. La segunda línea registra `MyDep` para `IMyDep2`. La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

Normalmente, el registro del servicio es independiente, excepto cuando se registran varias implementaciones del mismo tipo.

`IServiceCollection` es una colección de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>. En el código siguiente se muestra cómo agregar un servicio con un constructor:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

Los métodos `Add{LIFETIME}` usan el mismo enfoque. Por ejemplo, vea el [código fuente de AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).

### <a name="constructor-injection-behavior"></a>Comportamiento de inserción de constructor

Los servicios se pueden resolver mediante dos mecanismos:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * Permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.
  * Se utiliza con características de marcos, como [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro), controladores MVC y [enlazadores de modelos](xref:mvc/models/model-binding).

Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.

Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.

Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable. Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente. La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos. En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.

## <a name="lifetime-and-registration-options"></a>Opciones de registro y duración

Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador, `OperationId`. Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Las interfaces se implementan en la clase `Operation`. El constructor `Operation` genera los cuatro últimos caracteres de un GUID en caso de que no se proporcione uno:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes. El objeto `IndexModel` y el middleware de la aplicación de ejemplo solicita cada tipo de `IOperation` y registra la `OperationId`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

El middleware es similar al `IndexModel` y resuelve los mismos servicios:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Los servicios con ámbito se deben resolver en el método `InvokeAsync`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

La salida del registrador muestra:

* Los objetos *Transient* siempre son diferentes. El valor transitorio de `OperationId` es diferente en el `IndexModel` y el middleware.
* Los objetos *con ámbito* son iguales dentro de una solicitud, pero varían entre solicitudes.
* Los objetos *singleton* son los mismos para cada solicitud.

Para reducir la salida del registro, establezca "Logging:LogLevel:Microsoft:Error" en el archivo *appsettings.Development.json*:

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Llamada a servicios desde main

Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación. Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización:

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

El código anterior procede de [Agregar el inicializador](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) en el tutorial de Razor Pages.

En el siguiente ejemplo se indica cómo obtener un contexto para `IMyDependency` en `Program.Main`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Validación del ámbito

Cuando la aplicación se ejecuta en el [entorno de desarrollo](xref:fundamentals/environments) y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:

* Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.
* Los servicios con ámbito no se insertan directa o indirectamente en singletons.
* Los servicios transitorios no se insertan directa o indirectamente en singletons o servicios con ámbito.

El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. La vigencia del proveedor de servicios raíz es la misma que la de la aplicación cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.

De la eliminación de los servicios con ámbito se encarga el contenedor que los creó. Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación se cierra. Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.

Para más información, vea [Validación del ámbito](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Servicios de solicitud

Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación. Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.

Por lo general, la aplicación no debe usar estas propiedades directamente. En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias. Esto produce clases que son más fáciles de probar.

ASP.NET Core crea un ámbito por solicitud y `RequestServices` expone el proveedor de servicios de ámbito. Todos los servicios con ámbito son válidos mientras la solicitud está activa.

> [!NOTE]
> Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Diseño de servicios para la inserción de dependencias

Los procedimientos recomendados son:

* Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.
* Evitar clases y miembros estáticos y con estado. En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.
* Evitar la creación directa de instancias de clases dependientes dentro de los servicios. La creación directa de instancias se acopla al código de una implementación particular.
* Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.

Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla. Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.

### <a name="disposal-of-services"></a>Eliminación de servicios

El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea. Los servicios nunca deben desecharse mediante ningún código que haya resuelto el servicio de un contenedor. Si un tipo o generador se registra como singleton, el contenedor elimina el singleton.

En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

La consola de depuración muestra la siguiente salida después de cada actualización de la página de índice:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Servicios no creados por el contenedor de servicios
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
Observe el código siguiente:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

En el código anterior:

* El contenedor de servicios no crea las instancias de servicio.
* El marco de trabajo no conoce la duración prevista del servicio.
* El marco de trabajo no elimina los servicios de manera automática.
* Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Instrucciones de IDisposable para instancias transitorias y compartidas

#### <a name="transient-limited-lifetime"></a>Instancia transitoria, duración limitada

**Escenario**

La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:

* La instancia se resuelve en el ámbito raíz (contenedor raíz).
* La instancia se debe eliminar antes de que finalice el ámbito.

**Solución**

Use el patrón de fábrica para crear una instancia fuera del ámbito principal. En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final. Si el tipo final tiene otras dependencias, la fábrica puede:

* Recibir un <xref:System.IServiceProvider> en su constructor.
* Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.

#### <a name="shared-instance-limited-lifetime"></a>Instancia compartida, duración limitada

**Escenario**

La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.

**Solución**

Registre la instancia con una duración restringida. Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo. Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios. Elimine el ámbito cuando la duración deba finalizar.

#### <a name="general-idisposable-guidelines"></a>Instrucciones generales de IDisposable

* No registre instancias de <xref:System.IDisposable> con un ámbito transitorio. En su lugar, use el patrón de fábrica.
* No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz. La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.
* La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo. El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.
* Se deben usar ámbitos para controlar la duración de los servicios. Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.

## <a name="default-service-container-replacement"></a>Reemplazo del contenedor de servicios predeterminado

El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor. Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:

* Inserción de propiedades
* Inserción basada en nombres
* Contenedores secundarios
* Administración personalizada del ciclo de vida
* Compatibilidad con `Func<T>` para la inicialización diferida
* Registro basado en convenciones

Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Seguridad para subprocesos

Cree servicios de singleton seguros para subprocesos. Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.

El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos. Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.

## <a name="recommendations"></a>Recomendaciones

* No se admite la resolución de servicio basada en `async/await` y `Task`. C# no admite constructores asincrónicos. El patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.
* Evite almacenar datos y configuraciones directamente en el contenedor de servicios. Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios. La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options). Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto. Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.
* Evite el acceso estático a los servicios. Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).
* Mantenga los generadores de DI rápidos y sincrónicos.
* Evite el uso del *patrón del localizador de servicios*. Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:

  **Incorrecto:**

    ![Código incorrecto](dependency-injection/_static/bad.png)

  **Correcto**:

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
* Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución. Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
* Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* Evite las llamadas a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> en `ConfigureServices`. La llamada a `BuildServiceProvider` suele ocurrir cuando el desarrollador desea resolver un servicio en `ConfigureServices`. Por ejemplo, considere el caso en el que necesita obtener el `LoginPath` de la configuración. Agregue el código siguiente:

  ![código incorrecto llamando a BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  En la imagen anterior, al seleccionar la línea verde ondulada debajo de `services.BuildServiceProvider` se muestra la siguiente advertencia ASP0000:
    * ASP0000 Al llamar a "BuildServiceProvider" desde el código de aplicación, se crea una copia adicional de los servicios singleton. Considere alternativas como los servicios de inserción de dependencias como parámetros para "Configurar".

   La llamada a `BuildServiceProvider` crea un segundo contenedor que puede crear singletons rasgados y producir referencias a gráficos de objetos en varios contenedores. Una manera correcta de obtener `LoginPath` es usar el patrón de opción con DI:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* El contenedor captura a los servicios transitorios descartables para su eliminación. Esto puede convertirse en una pérdida de memoria si se resuelve desde el contenedor de nivel superior.
* Habilite la validación con ámbito para asegurarse de que la aplicación no tenga servicios con ámbito que capturen los singletons. Para más información, vea [Validación del ámbito](#scope-validation).

Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas. Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.

La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales. No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Patrones recomendados para los servicios multiinquilino en la inserción de dependencias

[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino. Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).

Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.

## <a name="framework-provided-services"></a>Servicios proporcionados por el marco de trabajo

El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC. Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host). Las aplicaciones basadas en una plantilla de ASP.NET Core tienen más de 250 servicios registrados por el marco. En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.

| Tipo de servicio | Período de duración |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Patrones de la Conferencia NDC para el desarrollo de aplicaciones de inserción de dependencias](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Cuatro formas de eliminar IDisposables en ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.

Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Información general sobre la inserción de dependencias

Una *dependencia* es cualquier objeto requerido por otro objeto. Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:

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

Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase. La clase `MyDependency` es una dependencia de la clase `IndexModel`:

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

La clase crea y depende directamente de la instancia `MyDependency`. Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:

* Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.
* Si `MyDependency` tiene dependencias, deben configurarse según la clase. En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.
* Esta implementación es difícil para realizar pruebas unitarias. La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.

La inserción de dependencias aborda estos problemas mediante:

* Uso de una interfaz o clase base para abstraer la implementación de dependencias.
* Registro de la dependencia en un contenedor de servicios. ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>. Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.
* *Inserción* del servicio en el constructor de la clase en la que se usa. El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.

En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor. No es raro usar la inserción de dependencias de forma encadenada. Cada dependencia solicitada a su vez solicita sus propias dependencias. El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto. El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.

`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios. `IMyDependency` está registrado en `Startup.ConfigureServices`. `ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.

El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`. El registro abarca la duración del servicio como la duración de una única solicitud. Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura). Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren. Se recomienda que las aplicaciones sigan esta convención. Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.

Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):

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

Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado. El campo de utiliza para acceder al servicio, según sea necesario en la clase.

En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Servicios insertados en Startup

Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Los servicios se pueden insertar en `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Servicios proporcionados por el marco de trabajo

El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC. Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host). No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco. En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.

| Tipo de servicio | Período de duración |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitorio |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registro de servicios adicionales con métodos de extensión

Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio. El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

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

Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.

## <a name="service-lifetimes"></a>Duraciones de servicios

Elija una duración adecuada para cada servicio registrado. Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:

### <a name="transient"></a>Transitorio

Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita. Esta duración funciona mejor para servicios sin estado ligeros.

En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.

### <a name="scoped"></a>Con ámbito

Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).

En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.

> [!WARNING]
> Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`. No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton. Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio. Cada solicitud posterior usa la misma instancia. Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio. No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.

En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.

> [!WARNING]
> Es peligroso resolver un servicio con ámbito desde un singleton. Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.

## <a name="service-registration-methods"></a>Métodos de registro del servicio

Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.

| Método | Automático<br>objeto<br>eliminación | Múltiple<br>implementaciones | Transferencia de argumentos |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddSingleton<IMyDep, MyDep>();` | Sí | Sí | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sí | Sí | Sí |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Ejemplo:<br>`services.AddSingleton<MyDep>();` | Sí | No | No |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Sí | Sí |
| `AddSingleton(new {IMPLEMENTATION})`<br>Ejemplos:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | No | No | Sí |

Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services). Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).

Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.

En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`. La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Para obtener más información, consulte:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*. A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios. Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo. Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.

En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`. La segunda línea registra `MyDep` para `IMyDep2`. La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportamiento de inserción de constructor

Los servicios se pueden resolver mediante dos mecanismos:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias. `ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.

Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.

Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.

Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable. Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente. La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos. En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.

## <a name="lifetime-and-registration-options"></a>Opciones de registro y duración

Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`. Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Las interfaces se implementan en la clase `Operation`. El constructor `Operation` genera un GUID en caso de que no se proporcione uno:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`. Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.

* Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`. `OperationService` recibe una instancia nueva de la clase `IOperationTransient`. La nueva instancia produce un objeto `OperationId` diferente.
* Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente. Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.
* Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`. Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).

La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales. El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`. Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Las dos salidas siguientes muestran el resultado de dos solicitudes:

**Primera solicitud:**

Operaciones del controlador:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operaciones `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Segunda solicitud:**

Operaciones del controlador:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operaciones `OperationService`:

Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:

* Los objetos *Transient* siempre son diferentes. El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente. Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.
* Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.
* Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Llamada a servicios desde main

Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación. Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización. En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:

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

## <a name="scope-validation"></a>Validación del ámbito

Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:

* Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.
* Los servicios con ámbito no se insertan directa o indirectamente en singletons.

El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.

De la eliminación de los servicios con ámbito se encarga el contenedor que los creó. Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran. Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.

Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>Servicios de solicitud

Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación. Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.

Por lo general, la aplicación no debe usar estas propiedades directamente. En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias. Esto produce clases que son más fáciles de probar.

> [!NOTE]
> Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Diseño de servicios para la inserción de dependencias

Los procedimientos recomendados son:

* Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.
* Evitar clases y miembros estáticos y con estado. En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.
* Evitar la creación directa de instancias de clases dependientes dentro de los servicios. La creación directa de instancias se acopla al código de una implementación particular.
* Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.

Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla. Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario. Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Eliminación de servicios

El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea. Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.

En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:

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

En el ejemplo siguiente:

* El contenedor de servicios no crea las instancias de servicio.
* El marco de trabajo no conoce la duración prevista del servicio.
* El marco de trabajo no elimina los servicios de manera automática.
* Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Instrucciones de IDisposable para instancias transitorias y compartidas

#### <a name="transient-limited-lifetime"></a>Instancia transitoria, duración limitada

**Escenario**

La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:

* La instancia se resuelve en el ámbito raíz.
* La instancia se debe eliminar antes de que finalice el ámbito.

**Solución**

Use el patrón de fábrica para crear una instancia fuera del ámbito principal. En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final. Si el tipo final tiene otras dependencias, la fábrica puede:

* Recibir un <xref:System.IServiceProvider> en su constructor.
* Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.

#### <a name="shared-instance-limited-lifetime"></a>Instancia compartida, duración limitada

**Escenario**

La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.

**Solución**

Registre la instancia con una duración restringida. Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo. Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios. Elimine el ámbito cuando la duración deba finalizar.

#### <a name="general-guidelines"></a>Instrucciones generales

* No registre instancias de <xref:System.IDisposable> con un ámbito transitorio. En su lugar, use el patrón de fábrica.
* No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz. La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.
* La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo. El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.
* Se deben usar ámbitos para controlar la duración de los servicios. Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.

## <a name="default-service-container-replacement"></a>Reemplazo del contenedor de servicios predeterminado

El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor. Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:

* Inserción de propiedades
* Inserción basada en nombres
* Contenedores secundarios
* Administración personalizada del ciclo de vida
* Compatibilidad con `Func<T>` para la inicialización diferida
* Registro basado en convenciones

Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Seguridad para subprocesos

Cree servicios de singleton seguros para subprocesos. Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.

El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos. Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.

## <a name="recommendations"></a>Recomendaciones

* No se admite la resolución de servicio basada en `async/await` y `Task`. C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.

* Evite almacenar datos y configuraciones directamente en el contenedor de servicios. Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios. La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options). Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto. Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.
* Evite el acceso estático a los servicios. Por ejemplo, evite escribir de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar.

* Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
  * No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:

    **Incorrecto:**

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
   
    **Correcto**:

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

* Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.
* Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas. Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.

La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales. No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Cuatro formas de eliminar IDisposables en ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
