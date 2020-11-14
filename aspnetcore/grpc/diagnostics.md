---
title: Registro y diagnóstico en gRPC en .NET
author: jamesnk
description: Obtenga información sobre cómo recopilar diagnósticos de la aplicación gRPC en .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060448"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="c0d44-103">Registro y diagnóstico en gRPC en .NET</span><span class="sxs-lookup"><span data-stu-id="c0d44-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="c0d44-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c0d44-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c0d44-105">En este artículo se proporcionan instrucciones para recopilar diagnósticos de una aplicación gRPC para ayudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="c0d44-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="c0d44-106">Temas cubiertos:</span><span class="sxs-lookup"><span data-stu-id="c0d44-106">Topics covered include:</span></span>

* <span data-ttu-id="c0d44-107">**Registro** : registros estructurados escritos en el [registro de .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="c0d44-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c0d44-108">Los marcos de trabajo de la aplicación usan <xref:Microsoft.Extensions.Logging.ILogger> para escribir registros, y los usuarios, para su propio registro en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="c0d44-109">**Seguimiento** : eventos relacionados con una operación escrita mediante `DiaganosticSource` y `Activity`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="c0d44-110">Los seguimientos del origen de diagnóstico se suelen usar para recopilar telemetría de la aplicación mediante bibliotecas como [Application Insights](/azure/azure-monitor/app/asp-net-core) y [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="c0d44-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="c0d44-111">**Métricas** : representación de medidas de datos a lo largo de intervalos de tiempo, por ejemplo, solicitudes por segundo.</span><span class="sxs-lookup"><span data-stu-id="c0d44-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="c0d44-112">Las métricas se emiten mediante `EventCounter` y se pueden observar con la herramienta de línea de comandos [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) o con [Application Insights](/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="c0d44-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="c0d44-113">Registro</span><span class="sxs-lookup"><span data-stu-id="c0d44-113">Logging</span></span>

<span data-ttu-id="c0d44-114">Los servicios gRPC y el cliente gRPC escriben registros mediante el [registro de .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="c0d44-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c0d44-115">Los registros son un buen punto de partida cuando se necesita depurar un comportamiento inesperado en las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c0d44-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="c0d44-116">Registro de servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="c0d44-117">Los registros del lado servidor pueden contener información confidencial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c0d44-118">**Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.</span><span class="sxs-lookup"><span data-stu-id="c0d44-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c0d44-119">Como los servicios gRPC se hospedan en ASP.NET Core, usa el sistema de registro de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0d44-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="c0d44-120">En la configuración predeterminada, gRPC registra muy poca información, pero esto se puede configurar.</span><span class="sxs-lookup"><span data-stu-id="c0d44-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="c0d44-121">Vea la documentación sobre el [registro de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obtener más información sobre la configuración del registro de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0d44-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="c0d44-122">gRPC agrega registros en la categoría `Grpc`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="c0d44-123">Para habilitar registros detallados de gRPC, configure los prefijos `Grpc` en el nivel `Debug` del archivo *appsettings.json* mediante la adición de los siguientes elementos a la subsección `LogLevel` de `Logging`:</span><span class="sxs-lookup"><span data-stu-id="c0d44-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="c0d44-124">También se puede configurar en *Startup.cs* con `ConfigureLogging`:</span><span class="sxs-lookup"><span data-stu-id="c0d44-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="c0d44-125">Si no usa la configuración basada en JSON, establezca el siguiente valor de configuración en el sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="c0d44-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="c0d44-126">Consulte la documentación del sistema de configuración para determinar cómo especificar valores de configuración anidados.</span><span class="sxs-lookup"><span data-stu-id="c0d44-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="c0d44-127">Por ejemplo, al usar variables de entorno, se usan dos caracteres `_` en lugar de `:` (por ejemplo, `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="c0d44-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="c0d44-128">Se recomienda usar el nivel `Debug` al recopilar diagnósticos más detallados para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="c0d44-129">El nivel `Trace` genera diagnósticos de nivel muy bajo y rara vez es necesario para diagnosticar problemas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="c0d44-130">Salida de registro de ejemplo</span><span class="sxs-lookup"><span data-stu-id="c0d44-130">Sample logging output</span></span>

<span data-ttu-id="c0d44-131">Este es un ejemplo de la salida de la consola en el nivel `Debug` de un servicio gRPC:</span><span class="sxs-lookup"><span data-stu-id="c0d44-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="c0d44-132">Acceso a los registros del lado servidor</span><span class="sxs-lookup"><span data-stu-id="c0d44-132">Access server-side logs</span></span>

<span data-ttu-id="c0d44-133">La forma de acceder a los registros del lado servidor depende del entorno que se ejecute.</span><span class="sxs-lookup"><span data-stu-id="c0d44-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="c0d44-134">Como aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="c0d44-134">As a console app</span></span>

<span data-ttu-id="c0d44-135">Si se ejecuta en una aplicación de consola, el [registrador de la consola](xref:fundamentals/logging/index#console) debe estar habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c0d44-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="c0d44-136">Los registros de gRPC aparecerán en la consola.</span><span class="sxs-lookup"><span data-stu-id="c0d44-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="c0d44-137">Otros entornos</span><span class="sxs-lookup"><span data-stu-id="c0d44-137">Other environments</span></span>

<span data-ttu-id="c0d44-138">Si la aplicación se implementa en otro entorno (por ejemplo, Docker, Kubernetes o un servicio de Windows), vea <xref:fundamentals/logging/index> para obtener más información sobre cómo configurar los proveedores de registro adecuados para el entorno.</span><span class="sxs-lookup"><span data-stu-id="c0d44-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="c0d44-139">Registro de clientes de gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="c0d44-140">Los registros del lado cliente pueden contener información confidencial de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c0d44-141">**Nunca** publique registros sin procesar de las aplicaciones de producción en foros públicos como GitHub.</span><span class="sxs-lookup"><span data-stu-id="c0d44-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c0d44-142">Para obtener los registros del cliente de .NET, puede establecer la propiedad `GrpcChannelOptions.LoggerFactory` cuando se crea el canal del cliente.</span><span class="sxs-lookup"><span data-stu-id="c0d44-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="c0d44-143">Si va a llamar a un servicio gRPC desde una aplicación ASP.NET Core, el generador del registrador se puede resolver desde la inserción de dependencias (DI):</span><span class="sxs-lookup"><span data-stu-id="c0d44-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="c0d44-144">Una manera alternativa de habilitar el registro de cliente consiste en usar la [fábrica de cliente de gRPC](xref:grpc/clientfactory) para crear el cliente.</span><span class="sxs-lookup"><span data-stu-id="c0d44-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="c0d44-145">Un cliente gRPC registrado con la fábrica de cliente y resuelto a partir de la inserción de dependencias usará de forma automática el registro configurado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="c0d44-146">Si la aplicación no usa inserción de dependencias, puede crear una instancia de `ILoggerFactory` con [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="c0d44-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="c0d44-147">Para acceder a este método, agregue el paquete [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="c0d44-148">Ámbitos de registro de cliente de gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-148">gRPC client log scopes</span></span>

<span data-ttu-id="c0d44-149">El cliente gRPC agrega un [ámbito de registro](../fundamentals/logging/index.md#log-scopes) a los registros realizados durante una llamada a gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="c0d44-150">El ámbito tiene metadatos relacionados con la llamada a gRPC:</span><span class="sxs-lookup"><span data-stu-id="c0d44-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="c0d44-151">**GrpcMethodType** : tipo de método de gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="c0d44-152">Los valores posibles son los nombres de la enumeración `Grpc.Core.MethodType`, por ejemplo, unario</span><span class="sxs-lookup"><span data-stu-id="c0d44-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="c0d44-153">**GrpcUri** : URI relativo del método gRPC, por ejemplo, /greet.Greeter/SayHellos.</span><span class="sxs-lookup"><span data-stu-id="c0d44-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="c0d44-154">Salida de registro de ejemplo</span><span class="sxs-lookup"><span data-stu-id="c0d44-154">Sample logging output</span></span>

<span data-ttu-id="c0d44-155">Este es un ejemplo de la salida de la consola en el nivel `Debug` de un cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="c0d44-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="c0d44-156">Traza</span><span class="sxs-lookup"><span data-stu-id="c0d44-156">Tracing</span></span>

<span data-ttu-id="c0d44-157">Los servicios y el cliente gRPC proporcionan información sobre las llamadas a gRPC mediante [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) y [Activity](/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="c0d44-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="c0d44-158">gRPC de .NET usa una actividad para representar una llamada a gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="c0d44-159">Los eventos de seguimiento se escriben en el origen de diagnóstico al iniciar y detener la actividad de la llamada a gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="c0d44-160">El seguimiento no captura información sobre cuándo se envían los mensajes a lo largo de la duración de las llamadas de streaming de gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="c0d44-161">Seguimiento de servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-161">gRPC service tracing</span></span>

<span data-ttu-id="c0d44-162">Los servicios gRPC se hospedan en ASP.NET Core, que notifica eventos sobre las solicitudes HTTP entrantes.</span><span class="sxs-lookup"><span data-stu-id="c0d44-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="c0d44-163">Los metadatos específicos de gRPC se agregan a los diagnósticos de solicitudes HTTP existentes que proporciona ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0d44-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="c0d44-164">El nombre del origen de diagnóstico es `Microsoft.AspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="c0d44-165">El nombre de la actividad es `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="c0d44-166">El nombre del método gRPC que invoca la llamada a gRPC se agrega como una etiqueta con el nombre `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="c0d44-167">Cuando se completa el código de estado de la llamada a gRPC, se agrega como una etiqueta con el nombre `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="c0d44-168">Seguimiento de clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-168">gRPC client tracing</span></span>

<span data-ttu-id="c0d44-169">El cliente gRPC de .NET usa `HttpClient` para realizar llamadas de gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="c0d44-170">Aunque `HttpClient` escribe eventos de diagnóstico, el cliente gRPC de .NET proporciona un origen de diagnóstico, una actividad y eventos personalizados para que se pueda recopilar información completa sobre una llamada a gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="c0d44-171">El nombre del origen de diagnóstico es `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="c0d44-172">El nombre de la actividad es `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="c0d44-173">El nombre del método gRPC que invoca la llamada a gRPC se agrega como una etiqueta con el nombre `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="c0d44-174">Cuando se completa el código de estado de la llamada a gRPC, se agrega como una etiqueta con el nombre `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="c0d44-175">Recopilación del seguimiento</span><span class="sxs-lookup"><span data-stu-id="c0d44-175">Collecting tracing</span></span>

<span data-ttu-id="c0d44-176">La forma más fácil de usar `DiagnosticSource` consiste en configurar una biblioteca de telemetría como [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) o [Application Insights](/azure/azure-monitor/app/asp-net-core) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="c0d44-177">La biblioteca procesará información sobre las llamadas a gRPC junto a otra telemetría de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c0d44-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="c0d44-178">El seguimiento se puede ver en un servicio administrado como Application Insights, o bien puede elegir ejecutar un sistema de seguimiento distribuido propio.</span><span class="sxs-lookup"><span data-stu-id="c0d44-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="c0d44-179">OpenTelemetry admite la exportación de datos de seguimiento a [Jaeger](https://www.jaegertracing.io/) y [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="c0d44-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="c0d44-180">`DiagnosticSource` puede consumir eventos de seguimiento en el código mediante `DiagnosticListener`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="c0d44-181">Para obtener información sobre cómo escuchar un origen de diagnóstico con código, vea la [Guía del usuario de DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="c0d44-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="c0d44-182">En la actualidad, las bibliotecas de telemetría no capturan telemetría `Grpc.Net.Client.GrpcOut` específica de gRPC.</span><span class="sxs-lookup"><span data-stu-id="c0d44-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="c0d44-183">El trabajo para mejorar las bibliotecas de telemetría que capturan este seguimiento está en curso.</span><span class="sxs-lookup"><span data-stu-id="c0d44-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="c0d44-184">Métricas</span><span class="sxs-lookup"><span data-stu-id="c0d44-184">Metrics</span></span>

<span data-ttu-id="c0d44-185">Las métricas son una representación de medidas de datos a lo largo de intervalos de tiempo, por ejemplo, solicitudes por segundo.</span><span class="sxs-lookup"><span data-stu-id="c0d44-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="c0d44-186">Los datos de las métricas permiten observar el estado de una aplicación en un general.</span><span class="sxs-lookup"><span data-stu-id="c0d44-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="c0d44-187">Las métricas gRPC de .NET se emiten mediante `EventCounter`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="c0d44-188">Métricas de servicios gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-188">gRPC service metrics</span></span>

<span data-ttu-id="c0d44-189">Las métricas de servicios gRPC se notifican en el origen del evento `Grpc.AspNetCore.Server`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="c0d44-190">NOMBRE</span><span class="sxs-lookup"><span data-stu-id="c0d44-190">Name</span></span>                      | <span data-ttu-id="c0d44-191">Descripción</span><span class="sxs-lookup"><span data-stu-id="c0d44-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="c0d44-192">Total de llamadas</span><span class="sxs-lookup"><span data-stu-id="c0d44-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="c0d44-193">Llamadas actuales</span><span class="sxs-lookup"><span data-stu-id="c0d44-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="c0d44-194">Total de errores de llamadas</span><span class="sxs-lookup"><span data-stu-id="c0d44-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="c0d44-195">Fecha límite de llamadas totales superada</span><span class="sxs-lookup"><span data-stu-id="c0d44-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="c0d44-196">Total de mensajes enviados</span><span class="sxs-lookup"><span data-stu-id="c0d44-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="c0d44-197">Total de mensajes recibidos</span><span class="sxs-lookup"><span data-stu-id="c0d44-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="c0d44-198">Total de llamadas no implementadas</span><span class="sxs-lookup"><span data-stu-id="c0d44-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="c0d44-199">ASP.NET Core también proporciona sus propias métricas en el origen del evento `Microsoft.AspNetCore.Hosting`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="c0d44-200">Métricas de clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="c0d44-200">gRPC client metrics</span></span>

<span data-ttu-id="c0d44-201">Las métricas de clientes gRPC se notifican en el origen del evento `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="c0d44-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="c0d44-202">NOMBRE</span><span class="sxs-lookup"><span data-stu-id="c0d44-202">Name</span></span>                      | <span data-ttu-id="c0d44-203">Descripción</span><span class="sxs-lookup"><span data-stu-id="c0d44-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="c0d44-204">Total de llamadas</span><span class="sxs-lookup"><span data-stu-id="c0d44-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="c0d44-205">Llamadas actuales</span><span class="sxs-lookup"><span data-stu-id="c0d44-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="c0d44-206">Total de errores de llamadas</span><span class="sxs-lookup"><span data-stu-id="c0d44-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="c0d44-207">Fecha límite de llamadas totales superada</span><span class="sxs-lookup"><span data-stu-id="c0d44-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="c0d44-208">Total de mensajes enviados</span><span class="sxs-lookup"><span data-stu-id="c0d44-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="c0d44-209">Total de mensajes recibidos</span><span class="sxs-lookup"><span data-stu-id="c0d44-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="c0d44-210">Observación de métricas</span><span class="sxs-lookup"><span data-stu-id="c0d44-210">Observe metrics</span></span>

<span data-ttu-id="c0d44-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) es una herramienta de supervisión de rendimiento diseñada para la investigación del rendimiento y la supervisión del estado de primer nivel ad hoc.</span><span class="sxs-lookup"><span data-stu-id="c0d44-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="c0d44-212">Puede supervisar una aplicación .NET con `Grpc.AspNetCore.Server` o `Grpc.Net.Client` como el nombre del proveedor.</span><span class="sxs-lookup"><span data-stu-id="c0d44-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="c0d44-213">Otra forma de observar las métricas de gRPC consiste en capturar los datos de contadores mediante el [paquete Microsoft.ApplicationInsights.EventCounterCollector](/azure/azure-monitor/app/eventcounters) de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c0d44-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="c0d44-214">Una vez realizada la instalación, Application Insights recopila contadores comunes de .NET en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="c0d44-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="c0d44-215">Los contadores de gRPC no se recopilan de forma predeterminada, pero App Insights se puede [personalizar para incluir contadores adicionales](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="c0d44-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="c0d44-216">Especifique en *Startup.cs* los contadores de gRPC que Application Insights debe recopilar:</span><span class="sxs-lookup"><span data-stu-id="c0d44-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs* :</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c0d44-217">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c0d44-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>