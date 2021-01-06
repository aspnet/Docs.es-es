---
title: Registro y diagnóstico en gRPC en .NET
author: jamesnk
description: Obtenga información sobre cómo recopilar diagnósticos de la aplicación gRPC en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060448"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registro y diagnóstico en gRPC en .NET

Por [James Newton-King](https://twitter.com/jamesnk)

En este artículo se proporcionan instrucciones para recopilar diagnósticos de una aplicación gRPC para ayudar a solucionar problemas. Temas cubiertos:

* **Registro**: registros estructurados escritos en el [registro de .NET Core](xref:fundamentals/logging/index). Los marcos de trabajo de la aplicación usan <xref:Microsoft.Extensions.Logging.ILogger> para escribir registros, y los usuarios, para su propio registro en una aplicación.
* **Seguimiento**: eventos relacionados con una operación escrita mediante `DiaganosticSource` y `Activity`. Los seguimientos del origen de diagnóstico se suelen usar para recopilar telemetría de la aplicación mediante bibliotecas como [Application Insights](/azure/azure-monitor/app/asp-net-core) y [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Métricas**: representación de medidas de datos a lo largo de intervalos de tiempo, por ejemplo, solicitudes por segundo. Las métricas se emiten mediante `EventCounter` y se pueden observar con la herramienta de línea de comandos [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) o con [Application Insights](/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Registro

Los servicios gRPC y el cliente gRPC escriben registros mediante el [registro de .NET Core](xref:fundamentals/logging/index). Los registros son un buen punto de partida cuando se necesita depurar un comportamiento inesperado en las aplicaciones.

### <a name="grpc-services-logging"></a>Registro de servicios gRPC

> [!WARNING]
> Los registros del lado servidor pueden contener información confidencial de la aplicación. **Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.

Como los servicios gRPC se hospedan en ASP.NET Core, usa el sistema de registro de ASP.NET Core. En la configuración predeterminada, gRPC registra muy poca información, pero esto se puede configurar. Vea la documentación sobre el [registro de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obtener más información sobre la configuración del registro de ASP.NET Core.

gRPC agrega registros en la categoría `Grpc`. Para habilitar registros detallados de gRPC, configure los prefijos `Grpc` en el nivel `Debug` del archivo *appsettings.json* mediante la adición de los siguientes elementos a la subsección `LogLevel` de `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

También se puede configurar en *Startup.cs* con `ConfigureLogging`:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Si no usa la configuración basada en JSON, establezca el siguiente valor de configuración en el sistema de configuración:

* `Logging:LogLevel:Grpc` = `Debug`

Consulte la documentación del sistema de configuración para determinar cómo especificar valores de configuración anidados. Por ejemplo, al usar variables de entorno, se usan dos caracteres `_` en lugar de `:` (por ejemplo, `Logging__LogLevel__Grpc`).

Se recomienda usar el nivel `Debug` al recopilar diagnósticos más detallados para la aplicación. El nivel `Trace` genera diagnósticos de nivel muy bajo y rara vez es necesario para diagnosticar problemas en la aplicación.

#### <a name="sample-logging-output"></a>Salida de registro de ejemplo

Este es un ejemplo de la salida de la consola en el nivel `Debug` de un servicio gRPC:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Acceso a los registros del lado servidor

La forma de acceder a los registros del lado servidor depende del entorno que se ejecute.

#### <a name="as-a-console-app"></a>Como aplicación de consola

Si se ejecuta en una aplicación de consola, el [registrador de la consola](xref:fundamentals/logging/index#console) debe estar habilitado de forma predeterminada. Los registros de gRPC aparecerán en la consola.

#### <a name="other-environments"></a>Otros entornos

Si la aplicación se implementa en otro entorno (por ejemplo, Docker, Kubernetes o un servicio de Windows), vea <xref:fundamentals/logging/index> para obtener más información sobre cómo configurar los proveedores de registro adecuados para el entorno.

### <a name="grpc-client-logging"></a>Registro de clientes de gRPC

> [!WARNING]
> Los registros del lado cliente pueden contener información confidencial de la aplicación. **Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.

Para obtener los registros del cliente de .NET, puede establecer la propiedad `GrpcChannelOptions.LoggerFactory` cuando se crea el canal del cliente. Si va a llamar a un servicio gRPC desde una aplicación ASP.NET Core, el generador del registrador se puede resolver desde la inserción de dependencias (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Una manera alternativa de habilitar el registro de cliente consiste en usar la [fábrica de cliente de gRPC](xref:grpc/clientfactory) para crear el cliente. Un cliente gRPC registrado con la fábrica de cliente y resuelto a partir de la inserción de dependencias usará de forma automática el registro configurado de la aplicación.

Si la aplicación no usa inserción de dependencias, puede crear una instancia de `ILoggerFactory` con [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Para acceder a este método, agregue el paquete [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) a la aplicación.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>Ámbitos de registro de cliente de gRPC

El cliente gRPC agrega un [ámbito de registro](../fundamentals/logging/index.md#log-scopes) a los registros realizados durante una llamada a gRPC. El ámbito tiene metadatos relacionados con la llamada a gRPC:

* **GrpcMethodType**: tipo de método de gRPC. Los valores posibles son los nombres de la enumeración `Grpc.Core.MethodType`, por ejemplo, unario
* **GrpcUri**: URI relativo del método gRPC, por ejemplo, /greet.Greeter/SayHellos.

#### <a name="sample-logging-output"></a>Salida de registro de ejemplo

Este es un ejemplo de la salida de la consola en el nivel `Debug` de un cliente gRPC:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Traza

Los servicios y el cliente gRPC proporcionan información sobre las llamadas a gRPC mediante [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) y [Activity](/dotnet/api/system.diagnostics.activity).

* gRPC de .NET usa una actividad para representar una llamada a gRPC.
* Los eventos de seguimiento se escriben en el origen de diagnóstico al iniciar y detener la actividad de la llamada a gRPC.
* El seguimiento no captura información sobre cuándo se envían los mensajes a lo largo de la duración de las llamadas de streaming de gRPC.

### <a name="grpc-service-tracing"></a>Seguimiento de servicios gRPC

Los servicios gRPC se hospedan en ASP.NET Core, que notifica eventos sobre las solicitudes HTTP entrantes. Los metadatos específicos de gRPC se agregan a los diagnósticos de solicitudes HTTP existentes que proporciona ASP.NET Core.

* El nombre del origen de diagnóstico es `Microsoft.AspNetCore`.
* El nombre de la actividad es `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * El nombre del método gRPC que invoca la llamada a gRPC se agrega como una etiqueta con el nombre `grpc.method`.
  * Cuando se completa el código de estado de la llamada a gRPC, se agrega como una etiqueta con el nombre `grpc.status_code`.

### <a name="grpc-client-tracing"></a>Seguimiento de clientes gRPC

El cliente gRPC de .NET usa `HttpClient` para realizar llamadas de gRPC. Aunque `HttpClient` escribe eventos de diagnóstico, el cliente gRPC de .NET proporciona un origen de diagnóstico, una actividad y eventos personalizados para que se pueda recopilar información completa sobre una llamada a gRPC.

* El nombre del origen de diagnóstico es `Grpc.Net.Client`.
* El nombre de la actividad es `Grpc.Net.Client.GrpcOut`.
  * El nombre del método gRPC que invoca la llamada a gRPC se agrega como una etiqueta con el nombre `grpc.method`.
  * Cuando se completa el código de estado de la llamada a gRPC, se agrega como una etiqueta con el nombre `grpc.status_code`.

### <a name="collecting-tracing"></a>Recopilación del seguimiento

La forma más fácil de usar `DiagnosticSource` consiste en configurar una biblioteca de telemetría como [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) o [Application Insights](/azure/azure-monitor/app/asp-net-core) en la aplicación. La biblioteca procesará información sobre las llamadas a gRPC junto a otra telemetría de la aplicación.

El seguimiento se puede ver en un servicio administrado como Application Insights, o bien puede elegir ejecutar un sistema de seguimiento distribuido propio. OpenTelemetry admite la exportación de datos de seguimiento a [Jaeger](https://www.jaegertracing.io/) y [Zipkin](https://zipkin.io/).

`DiagnosticSource` puede consumir eventos de seguimiento en el código mediante `DiagnosticListener`. Para obtener información sobre cómo escuchar un origen de diagnóstico con código, vea la [Guía del usuario de DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> En la actualidad, las bibliotecas de telemetría no capturan telemetría `Grpc.Net.Client.GrpcOut` específica de gRPC. El trabajo para mejorar las bibliotecas de telemetría que capturan este seguimiento está en curso.

## <a name="metrics"></a>Métricas

Las métricas son una representación de medidas de datos a lo largo de intervalos de tiempo, por ejemplo, solicitudes por segundo. Los datos de las métricas permiten observar el estado de una aplicación en un general. Las métricas gRPC de .NET se emiten mediante `EventCounter`.

### <a name="grpc-service-metrics"></a>Métricas de servicios gRPC

Las métricas de servicios gRPC se notifican en el origen del evento `Grpc.AspNetCore.Server`.

| NOMBRE                      | Descripción                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de llamadas                   |
| `current-calls`           | Llamadas actuales                 |
| `calls-failed`            | Total de errores de llamadas            |
| `calls-deadline-exceeded` | Fecha límite de llamadas totales superada |
| `messages-sent`           | Total de mensajes enviados           |
| `messages-received`       | Total de mensajes recibidos       |
| `calls-unimplemented`     | Total de llamadas no implementadas     |

ASP.NET Core también proporciona sus propias métricas en el origen del evento `Microsoft.AspNetCore.Hosting`.

### <a name="grpc-client-metrics"></a>Métricas de clientes gRPC

Las métricas de clientes gRPC se notifican en el origen del evento `Grpc.Net.Client`.

| NOMBRE                      | Descripción                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de llamadas                   |
| `current-calls`           | Llamadas actuales                 |
| `calls-failed`            | Total de errores de llamadas            |
| `calls-deadline-exceeded` | Fecha límite de llamadas totales superada |
| `messages-sent`           | Total de mensajes enviados           |
| `messages-received`       | Total de mensajes recibidos       |

### <a name="observe-metrics"></a>Observación de métricas

[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) es una herramienta de supervisión de rendimiento diseñada para la investigación del rendimiento y la supervisión del estado de primer nivel ad hoc. Puede supervisar una aplicación .NET con `Grpc.AspNetCore.Server` o `Grpc.Net.Client` como el nombre del proveedor.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Otra forma de observar las métricas de gRPC consiste en capturar los datos de contadores mediante el [paquete Microsoft.ApplicationInsights.EventCounterCollector](/azure/azure-monitor/app/eventcounters) de Application Insights. Una vez realizada la instalación, Application Insights recopila contadores comunes de .NET en tiempo de ejecución. Los contadores de gRPC no se recopilan de forma predeterminada, pero App Insights se puede [personalizar para incluir contadores adicionales](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Especifique en *Startup.cs* los contadores de gRPC que Application Insights debe recopilar:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>