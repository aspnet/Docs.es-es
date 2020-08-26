---
title: Inserción de dependencias de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo las aplicaciones Blazor pueden insertar servicios en componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 3dc15f5efcc8f48a809bf9132588fb38732a7b35
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628292"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>Inserción de dependencias de Blazor de ASP.NET Core

Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)

Blazor admite la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection). Las aplicaciones pueden usar servicios integrados mediante su inserción en componentes. Las aplicaciones también pueden definir y registrar servicios personalizados y hacer que estén disponibles en toda la aplicación a través de la inserción de dependencias.

La inserción de dependencias es una técnica para acceder a los servicios configurados en una ubicación central. Esto puede ser útil en aplicaciones Blazor para:

* Compartir una única instancia de una clase de servicio entre varios componentes, lo que se conoce como servicio *singleton*.
* Desacoplar componentes de clases de servicio concretas mediante abstracciones de referencia. Por ejemplo, considere una interfaz `IDataAccess` para acceder a los datos de la aplicación. La interfaz se implementa mediante una clase `DataAccess` concreta y se registra como un servicio en el contenedor de servicios de la aplicación. Cuando un componente usa la inserción de dependencias para recibir una implementación de `IDataAccess`, el componente no se acopla al tipo concreto. La implementación se puede intercambiar, posiblemente para una implementación ficticia en pruebas unitarias.

## <a name="default-services"></a>Servicios predeterminados

Los servicios predeterminados se agregan de forma automática a la colección de servicios de la aplicación.

| web de Office | Período de duración | Descripción |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Con ámbito | Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.<br><br>La instancia de <xref:System.Net.Http.HttpClient> en una aplicación Blazor WebAssembly usa el explorador para administrar el tráfico HTTP en segundo plano.<br><br>Las aplicaciones Blazor Server no incluyen un objeto <xref:System.Net.Http.HttpClient> configurado como servicio de forma predeterminada. Proporcione un objeto <xref:System.Net.Http.HttpClient> a una aplicación Blazor Server.<br><br>Para obtener más información, vea <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton (Blazor WebAssembly)<br>Con ámbito (Blazor Server) | Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript. Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton (Blazor WebAssembly)<br>Con ámbito (Blazor Server) | Contiene asistentes para trabajar con URI y el estado de navegación. Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla. Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.

## <a name="add-services-to-an-app"></a>Adición de servicios a una aplicación

### Blazor WebAssembly

Configure los servicios de la colección de servicios de la aplicación en el método `Main` de `Program.cs`. En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

Una vez que se ha compilado el host, se puede acceder a los servicios desde el ámbito de inserción de dependencias raíz antes de que se representen los componentes. Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

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
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

Después de crear una aplicación, examine el método `Startup.ConfigureServices`:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

Al método <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de descriptor de servicio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Para agregar los servicios al método `ConfigureServices`, se proporcionan descriptores de servicio a la colección de servicios. En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Duración del servicio

Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.

| Período de duración | Descripción |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Las aplicaciones Blazor WebAssembly no tienen actualmente un concepto de ámbitos de inserción de dependencias. Los servicios registrados con `Scoped` se comportan como servicios `Singleton`. Pero el modelo de hospedaje de Blazor Server admite la duración `Scoped`. En las aplicaciones Blazor Server, el ámbito del registro de un servicio con ámbito es la *conexión*. Por este motivo, se prefiere el uso de servicios con ámbito para los servicios que deben tener el ámbito del usuario actual, aunque la intención actual sea ejecutar el lado cliente en el explorador. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | La inserción de dependencias crea una *sola instancia* del servicio. Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio. |

El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core. Para obtener más información, vea <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicitud de un servicio en un componente

Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [\@inject](xref:mvc/views/razor#inject) de Razor. [`@inject`](xref:mvc/views/razor#inject) tiene dos parámetros:

* Tipo: el tipo de servicio que se va a insertar.
* Propiedad: el nombre de la propiedad que recibe el servicio de aplicación insertado. La propiedad no requiere la creación manual. El compilador crea la propiedad.

Para obtener más información, vea <xref:mvc/views/dependency-injection>.

Use varias instrucciones [`@inject`](xref:mvc/views/razor#inject) para insertar distintos servicios.

En el ejemplo siguiente se muestra cómo utilizar [`@inject`](xref:mvc/views/razor#inject). El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente. Observe cómo el código solo usa la abstracción de `IDataAccess`:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

De forma interna, la propiedad generada (`DataRepository`) usa el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute). Normalmente, este atributo no se usa de manera directa. Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute):

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

Es posible que los servicios complejos requieran servicios adicionales. En el ejemplo anterior, `DataAccess` podría requerir el servicio predeterminado <xref:System.Net.Http.HttpClient>. [`@inject`](xref:mvc/views/razor#inject) (o el atributo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)) no está disponible para su uso en los servicios. En su lugar se debe usar la *inserción de constructores*. Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio. Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia. En el ejemplo siguiente, el constructor recibe <xref:System.Net.Http.HttpClient> a través de DI. <xref:System.Net.Http.HttpClient> es un servicio predeterminado.

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
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

  Los servicios de inserción de dependencias insertados en el componente mediante [`@inject`](xref:mvc/views/razor#inject) o el atributo[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) no se crean en el ámbito del componente. Para usar el ámbito del componente, los servicios se deben resolver mediante <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> o <xref:System.IServiceProvider.GetService%2A>. Los servicios que se resuelvan mediante el proveedor de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> reciben sus dependencias desde ese mismo ámbito.

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

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

En el ejemplo siguiente se detecta `TransientDisposable` (`Program.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

En el ejemplo siguiente se detecta `TransientDependency` (`Startup.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/dependency-injection>
* [Instrucciones de `IDisposable` para instancias transitorias y compartidas](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
