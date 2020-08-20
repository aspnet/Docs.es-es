---
title: Tareas en segundo plano con servicios hospedados en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo implementar tareas en segundo plano con servicios hospedados en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 04841eb4f6adfec76020d3fe61601037c3fc0733
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635351"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tareas en segundo plano con servicios hospedados en ASP.NET Core

Por [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

En ASP.NET Core, las tareas en segundo plano se pueden implementar como *servicios hospedados*. Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>. En este tema se incluyen tres ejemplos de servicio hospedado:

* Una tarea en segundo plano que se ejecuta según un temporizador.
* Un servicio hospedado que activa un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes). El servicio con ámbito puede usar la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).
* Tareas en segundo plano en cola que se ejecutan en secuencia.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Plantilla Worker Service

La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración. Una aplicación creada a partir de la plantilla Worker Service especifica el SDK de trabajo en su archivo del proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Para usar la plantilla como base de una aplicación de servicios hospedados:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Package

Una aplicación basada en la plantilla Worker Service usa el SDK de `Microsoft.NET.Sdk.Worker` y tiene una referencia de paquete explícita al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting). Por ejemplo, consulte el archivo del proyecto de la aplicación de ejemplo (*BackgroundTasksSample.csproj*).

En el caso de las aplicaciones web que usan el SDK de `Microsoft.NET.Sdk.Web`, desde el marco compartido se hace una referencia implícita al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting). No se requiere una referencia de paquete explícita en el archivo del proyecto de la aplicación.

## <a name="ihostedservice-interface"></a>Interfaz IHostedService

La interfaz <xref:Microsoft.Extensions.Hosting.IHostedService> define dos métodos para los objetos administrados por el host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la lógica para iniciar la tarea en segundo plano. Se llama a `StartAsync`*antes de que*:

  * La canalización de procesamiento de solicitudes de la aplicación está configurada (`Startup.Configure`).
  * El servidor se haya iniciado y [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) se haya activado.

  El comportamiento predeterminado se puede cambiar para que el `StartAsync` del servicio hospedado se ejecute después de que se haya configurado la canalización de la aplicación y se haya llamado a `ApplicationStarted`. Para cambiar el comportamiento predeterminado, agregue el servicio hospedado (`VideosWatcher` en el ejemplo siguiente) después de llamar a `ConfigureWebHostDefaults`:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): se desencadena cuando el host está realizando un cierre estable. `StopAsync` contiene la lógica para finalizar la tarea en segundo plano. Implemente <xref:System.IDisposable> y los [finalizadores (destructores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para desechar los recursos no administrados.

  El token de cancelación tiene un tiempo de espera predeterminado de cinco segundos para indicar que el proceso de cierre ya no debería ser estable. Cuando se solicita la cancelación en el token:

  * Se deben anular las operaciones restantes en segundo plano que realiza la aplicación.
  * Los métodos llamados en `StopAsync` deberían devolver contenido al momento.

  No obstante, las tareas no se abandonan después de solicitar la cancelación, sino que el autor de la llamada espera a que se completen todas las tareas.

  Si la aplicación se cierra inesperadamente (por ejemplo, porque se produzca un error en el proceso de la aplicación), puede que no sea posible llamar a `StopAsync`. Por lo tanto, los métodos llamados o las operaciones llevadas a cabo en `StopAsync` podrían no producirse.

  Para ampliar el tiempo de espera predeterminado de apagado de 5 segundos, establezca:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> cuando se usa el host genérico. Para obtener más información, vea <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Configuración de los valores de host de tiempo de espera de apagado cuando se usa el host web. Para obtener más información, vea <xref:fundamentals/host/web-host#shutdown-timeout>.

El servicio hospedado se activa una vez al inicio de la aplicación y se cierra de manera estable cuando dicha aplicación se cierra. Si se produce un error durante la ejecución de una tarea en segundo plano, hay que llamar a `Dispose`, aun cuando no se haya llamado a `StopAsync`.

## <a name="backgroundservice-base-class"></a>Clase base BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService> es una clase base para implementar un <xref:Microsoft.Extensions.Hosting.IHostedService> de larga duración.

Se llama a [ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) para ejecutar el servicio en segundo plano. La implementación devuelve <xref:System.Threading.Tasks.Task>, que representa toda la duración del servicio en segundo plano. No se inicia ningún servicio hasta que [ExecuteAsync se convierte en asincrónico](https://github.com/dotnet/extensions/issues/2149), mediante una llamada a `await`. Evite realizar un trabajo de inicialización de bloqueo prolongado en `ExecuteAsync`. El host se bloquea en [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) a la espera de que `ExecuteAsync` se complete.

El token de cancelación se desencadena cuando se llama a [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*). La implementación de `ExecuteAsync` debe finalizar rápidamente cuando se active el token de cancelación para cerrar correctamente el servicio. De lo contrario, el servicio se cierra de manera abrupta durante el tiempo de expiración del cierre. Para más información, consulte la sección sobre la [interfaz IHostedService](#ihostedservice-interface).

## <a name="timed-background-tasks"></a>Tareas en segundo plano temporizadas

Una tarea en segundo plano temporizada hace uso de la clase [System.Threading.Timer](xref:System.Threading.Timer). El temporizador activa el método `DoWork` de la tarea. El temporizador está deshabilitado en `StopAsync` y se desecha cuando el contenedor de servicios se elimina en `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer> no espera a que finalicen las ejecuciones anteriores de `DoWork`, por lo que es posible que el enfoque mostrado no sea adecuado para todos los escenarios. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) se usa para incrementar el contador de ejecución como una operación atómica, lo que garantiza que varios subprocesos no actualicen `executionCount` simultáneamente.

El servicio se registra en `IHostBuilder.ConfigureServices` (*Program.cs*) con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumir un servicio con ámbito en una tarea en segundo plano

Para usar [servicios con ámbito](xref:fundamentals/dependency-injection#service-lifetimes) dentro de un [BackgroundService](#backgroundservice-base-class), cree un ámbito. No se crean ámbitos de forma predeterminada para los servicios hospedados.

El servicio de tareas en segundo plano con ámbito contiene la lógica de la tarea en segundo plano. En el ejemplo siguiente:

* El servicio es asincrónico. El método `DoWork` devuelve un objeto `Task`. Para fines de demostración, se espera un retraso de diez segundos en el método `DoWork`.
* <xref:Microsoft.Extensions.Logging.ILogger> se inserta en el servicio.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

El servicio hospedado crea un ámbito con el fin de resolver el servicio de tareas en segundo plano con ámbito para llamar a su método `DoWork`. `DoWork` devuelve `Task`, que se espera en `ExecuteAsync`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Los servicios se registran en `IHostBuilder.ConfigureServices` (*Program.cs*). El servicio hospedado se registra en con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tareas en segundo plano en cola

Una cola de tareas en segundo plano se basa en <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> de .NET 4.x:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

En el ejemplo `QueueHostedService` siguiente:

* El método `BackgroundProcessing` devuelve `Task`, que se espera en `ExecuteAsync`.
* Las tareas en segundo plano que están en cola se quitan de ella y se ejecutan en `BackgroundProcessing`.
* Se esperan elementos de trabajo antes de que el servicio se detenga en `StopAsync`.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Un servicio `MonitorLoop` controla las tareas de puesta en cola para el servicio hospedado cada vez que se selecciona la tecla `w` en un dispositivo de entrada:

* `IBackgroundTaskQueue` se inserta en el servicio `MonitorLoop`.
* Se llama a `IBackgroundTaskQueue.QueueBackgroundWorkItem` para poner en cola el elemento de trabajo.
* El elemento de trabajo simula una tarea en segundo plano de larga duración:
  * Se ejecutan tres retrasos de 5 segundos (`Task.Delay`).
  * Una instrucción `try-catch` captura <xref:System.OperationCanceledException> si se cancela la tarea.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Los servicios se registran en `IHostBuilder.ConfigureServices` (*Program.cs*). El servicio hospedado se registra en con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop` se inicia en `Program.Main`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En ASP.NET Core, las tareas en segundo plano se pueden implementar como *servicios hospedados*. Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>. En este tema se incluyen tres ejemplos de servicio hospedado:

* Una tarea en segundo plano que se ejecuta según un temporizador.
* Un servicio hospedado que activa un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes). El servicio con ámbito puede usar la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).
* Tareas en segundo plano en cola que se ejecutan en secuencia.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="package"></a>Package

Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interfaz IHostedService

Los servicios hospedados implementan la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>. Esta interfaz define dos métodos para los objetos administrados por el host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la lógica para iniciar la tarea en segundo plano. Al utilizar el [host web](xref:fundamentals/host/web-host), se llama a `StartAsync` después de que el servidor se haya iniciado y se haya activado [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*). Al utilizar el [host genérico](xref:fundamentals/host/generic-host), se llama a `StartAsync` antes de que se desencadene `ApplicationStarted`.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): se desencadena cuando el host está realizando un cierre estable. `StopAsync` contiene la lógica para finalizar la tarea en segundo plano. Implemente <xref:System.IDisposable> y los [finalizadores (destructores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para desechar los recursos no administrados.

  El token de cancelación tiene un tiempo de espera predeterminado de cinco segundos para indicar que el proceso de cierre ya no debería ser estable. Cuando se solicita la cancelación en el token:

  * Se deben anular las operaciones restantes en segundo plano que realiza la aplicación.
  * Los métodos llamados en `StopAsync` deberían devolver contenido al momento.

  No obstante, las tareas no se abandonan después de solicitar la cancelación, sino que el autor de la llamada espera a que se completen todas las tareas.

  Si la aplicación se cierra inesperadamente (por ejemplo, porque se produzca un error en el proceso de la aplicación), puede que no sea posible llamar a `StopAsync`. Por lo tanto, los métodos llamados o las operaciones llevadas a cabo en `StopAsync` podrían no producirse.

  Para ampliar el tiempo de espera predeterminado de apagado de 5 segundos, establezca:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> cuando se usa el host genérico. Para obtener más información, vea <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Configuración de los valores de host de tiempo de espera de apagado cuando se usa el host web. Para obtener más información, vea <xref:fundamentals/host/web-host#shutdown-timeout>.

El servicio hospedado se activa una vez al inicio de la aplicación y se cierra de manera estable cuando dicha aplicación se cierra. Si se produce un error durante la ejecución de una tarea en segundo plano, hay que llamar a `Dispose`, aun cuando no se haya llamado a `StopAsync`.

## <a name="timed-background-tasks"></a>Tareas en segundo plano temporizadas

Una tarea en segundo plano temporizada hace uso de la clase [System.Threading.Timer](xref:System.Threading.Timer). El temporizador activa el método `DoWork` de la tarea. El temporizador está deshabilitado en `StopAsync` y se desecha cuando el contenedor de servicios se elimina en `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer> no espera a que finalicen las ejecuciones anteriores de `DoWork`, por lo que es posible que el enfoque mostrado no sea adecuado para todos los escenarios.

El servicio se registra en `Startup.ConfigureServices` con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumir un servicio con ámbito en una tarea en segundo plano

Para usar [servicios con ámbito](xref:fundamentals/dependency-injection#service-lifetimes) en un elemento `IHostedService`, cree un ámbito. No se crean ámbitos de forma predeterminada para los servicios hospedados.

El servicio de tareas en segundo plano con ámbito contiene la lógica de la tarea en segundo plano. En el siguiente ejemplo, <xref:Microsoft.Extensions.Logging.ILogger> se inserta en el servicio:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

El servicio hospedado crea un ámbito con objeto de resolver el servicio de tareas en segundo plano con ámbito para llamar a su método `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Los servicios se registran en `Startup.ConfigureServices`. La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tareas en segundo plano en cola

La cola de tareas en segundo plano se basa en <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> de .NET Framework 4.x ([está previsto que se integre en ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

En `QueueHostedService`, las tareas en segundo plano en la cola se quitan de la cola y se ejecutan como un servicio [BackgroundService](#backgroundservice-base-class), que es una clase base para implementar `IHostedService` de ejecución prolongada:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Los servicios se registran en `Startup.ConfigureServices`. La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

En la clase de modelo de página de índice:

* Se inserta `IBackgroundTaskQueue` en el constructor y se asigna a `Queue`.
* Se inserta <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> y se asigna a `_serviceScopeFactory`. Se usa el generador se para crear instancias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que se usa para crear servicios dentro de un ámbito. Se crea un ámbito para poder usar el elemento `AppDbContext` de la aplicación (un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes)) para escribir registros de base de datos en `IBackgroundTaskQueue` (un servicio de singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Cuando se hace clic en el botón **Agregar tarea** en la página de índice, se ejecuta el método `OnPostAddTask`. Se llama a `QueueBackgroundWorkItem` para poner en cola un elemento de trabajo:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Implementar tareas en segundo plano en microservicios con IHostedService y la clase BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Ejecución de tareas en segundo plano con WebJobs en Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
