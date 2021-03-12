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
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109681"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Inserción de dependencias de Blazor de ASP.NET Core

Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)

[Inserción de dependencias (DI)](xref:fundamentals/dependency-injection) es una técnica para acceder a los servicios configurados en una ubicación central:

* Los servicios registrados en el marco se pueden insertar directamente en los componentes de aplicaciones de Blazor.
* Las aplicaciones de Blazor definen y registran servicios personalizados y los ponen a disposición de toda la aplicación a través de la inserción de dependencias.

## <a name="default-services"></a>Servicios predeterminados

Los servicios que se muestran en la tabla siguiente se usan normalmente en aplicaciones de Blazor.

| web de Office | Período de duración | Descripción |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Con ámbito | <p>Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</p><p>La instancia de <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.</p><p>Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada. Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.</p><p>Para obtener más información, vea <xref:blazor/call-web-api>.</p><p>Un <xref:System.Net.Http.HttpClient> se registra como un servicio con ámbito, no como singleton. Para más información, consulte la sección [Duración del servicio](#service-lifetime).</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Con ámbito</p> | Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript. Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Con ámbito</p> | Contiene asistentes para trabajar con URI y el estado de navegación. Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla. Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.

## <a name="add-services-to-an-app"></a>Adición de servicios a una aplicación

::: zone pivot="webassembly"

Configure los servicios de la colección de servicios de la aplicación en el método `Program.Main` de `Program.cs`. En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

Después de compilar el host, los servicios están disponibles en el ámbito de inserción de dependencias raíz antes de que se representen los componentes. Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

El host también proporciona una instancia de configuración central para la aplicación. En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, `appsettings.json`) a `InitializeWeatherAsync`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
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

::: zone-end

::: zone pivot="server"

Después de crear una aplicación, examine el método `Startup.ConfigureServices` en `Startup.cs`:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de [descriptor de servicio](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor). Para agregar los servicios al método `ConfigureServices`, se proporcionan descriptores de servicio a la colección de servicios. En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Duración del servicio

Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.

| Período de duración | Descripción |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Las aplicaciones Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias. Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</p><p>El modelo de hospedaje de Blazor Server admite la duración `Scoped` a través de las solicitudes HTTP, pero no a través de los mensajes de circuito/conexión de SignalR entre los componentes cargados en el cliente. Por lo general, Razor Razor o la parte de MVC de la aplicación trata los servicios con ámbito y vuelve a crear los servicios en *cada solicitud HTTP* al navegar entre páginas o vistas, o desde una página o vista a un componente. Los servicios con ámbito no se reconstruyen al navegar entre los componentes del cliente, donde la comunicación con el servidor se realiza a través de la conexión SignalR del circuito del usuario, no a través de solicitudes HTTP. En los escenarios de componentes del cliente siguientes, los servicios con ámbito se reconstruyen porque se crea un circuito nuevo para el usuario:</p><ul><li>El usuario cierra la ventana del explorador. El usuario abre una ventana nueva y vuelve a la aplicación.</li><li>El usuario cierra la última pestaña de la aplicación en una ventana del explorador. El usuario abre una pestaña nueva y vuelve a la aplicación.</li><li>El usuario selecciona el botón de recargar o actualizar del explorador.</li></ul><p>Para más información sobre cómo conservar el estado del usuario en los distintos servicios con ámbito en las aplicaciones Blazor Server, consulte <xref:blazor/hosting-models?pivots=server>.</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La inserción de dependencias crea una *sola instancia* del servicio. Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio. |

El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core. Para obtener más información, vea <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicitud de un servicio en un componente

Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [`@inject`](xref:mvc/views/razor#inject) de Razor, que tiene dos parámetros:

* Tipo: el tipo de servicio que se va a insertar.
* Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado. La propiedad no requiere la creación manual. El compilador crea la propiedad.

Para obtener más información, vea <xref:mvc/views/dependency-injection>.

Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.

En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject). El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente. Observe cómo el código solo usa la abstracción de `IDataAccess`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

De forma interna, la propiedad generada (`DataRepository`) usa el [atributo `[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute). Normalmente, este atributo no se usa de manera directa. Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el [atributo `[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

En los componentes derivados de la clase base, la directiva [`@inject`](xref:mvc/views/razor#inject) no es necesaria. Es suficiente con el objeto <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la clase base:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Uso de la inserción de dependencias en servicios

Es posible que los servicios complejos requieran servicios adicionales. En el ejemplo siguiente, `DataAccess` requiere el servicio predeterminado <xref:System.Net.Http.HttpClient>. [`@inject`](xref:mvc/views/razor#inject) (o el [atributo `[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios. En su lugar se debe usar la *inserción de constructores*. Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio. Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia. En el ejemplo siguiente, el constructor recibe <xref:System.Net.Http.HttpClient> a través de DI. <xref:System.Net.Http.HttpClient> es un servicio predeterminado.

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

Requisitos previos para la inserción de constructores:

* Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias. Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.
* El constructor aplicable debe ser `public`.
* Debe existir un constructor aplicable. En caso de ambigüedad, la inserción de dependencias inicia una excepción.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias

En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual. Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios. En las aplicaciones Blazor Server, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado. En las aplicaciones Blazor WebAssembly, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.

> [!NOTE]
> Para detectar servicios transitorios descartables en una aplicación, consulte la sección [Detección de transitorios descartables](#detect-transient-disposables).

Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un tipo abstracto derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente. Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente. Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente. Esto puede ser útil para servicios que:

* Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.
* No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.

Hay dos versiones del tipo <xref:Microsoft.AspNetCore.Components.OwningComponentBase> disponibles:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> es un elemento secundario abstracto y descartable del tipo <xref:Microsoft.AspNetCore.Components.ComponentBase> con una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> protegida de tipo <xref:System.IServiceProvider>. Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.

  Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el [atributo`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente. Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>. Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> y agrega una propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito. Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de <xref:System.IServiceProvider> cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente. La propiedad <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Uso de un objeto DbContext de Entity Framework Core (EF Core) desde la inserción de dependencias

Para obtener más información, vea <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Detección de transitorios descartables

En los siguientes ejemplos se muestra cómo detectar servicios transitorios descartables en una aplicación que debe usar <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. Para obtener más información, consulte la sección [Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias](#utility-base-component-classes-to-manage-a-di-scope).

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

En el ejemplo siguiente se detecta `TransientDisposable` (`Program.cs`):

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

Agregue el espacio de nombres de <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> a `Program.cs`:

```csharp
using Microsoft.Extensions.DependencyInjection;
```

En `Program.CreateHostBuilder` de `Program.cs`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

En el ejemplo siguiente se detecta `TransientDependency` (`Startup.cs`):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

La aplicación puede registrar transitorios descartables sin producir una excepción. Sin embargo, intentar resolver un transitorio descartable resulta en una clase <xref:System.InvalidOperationException>, como se muestra en el ejemplo siguiente.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

Navegue al componente `TransientDisposable` en `/transient-disposable`, y se lanza una clase <xref:System.InvalidOperationException> cuando el marco intenta construir una instancia de `TransientDisposable`:

> System.InvalidOperationException: intentando resolver el servicio del transitorio descartable TransientDisposable en el ámbito incorrecto. Use una clase base de componente "OwningComponentBase\<T>" para el servicio "T" que está intentando resolver.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/dependency-injection>
* [Instrucciones de `IDisposable` para instancias transitorias y compartidas](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
