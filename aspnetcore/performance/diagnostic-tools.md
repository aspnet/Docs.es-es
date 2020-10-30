---
title: Herramientas de diagnóstico de rendimiento
author: mjrousos
description: Herramientas útiles para diagnosticar problemas de rendimiento en aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060864"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="817b7-103">Herramientas de diagnóstico de rendimiento</span><span class="sxs-lookup"><span data-stu-id="817b7-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="817b7-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="817b7-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="817b7-105">En este artículo se enumeran las herramientas para diagnosticar problemas de rendimiento en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="817b7-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="817b7-106">Herramientas de diagnóstico de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="817b7-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="817b7-107">Las [herramientas de generación de perfiles y diagnóstico](/visualstudio/profiling) integradas en Visual Studio son un buen lugar para empezar a investigar los problemas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="817b7-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="817b7-108">Estas herramientas son eficaces y prácticas de uso desde el entorno de desarrollo de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="817b7-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="817b7-109">Las herramientas permiten el análisis del uso de la CPU, el uso de la memoria y los eventos de rendimiento en ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="817b7-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="817b7-110">La generación de perfiles facilita la generación de perfiles en el tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="817b7-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="817b7-111">Puede encontrar más información en la [documentación de Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="817b7-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="817b7-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="817b7-112">Application Insights</span></span>

<span data-ttu-id="817b7-113">[Application Insights](/azure/application-insights/app-insights-overview) proporciona datos de rendimiento detallados para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="817b7-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="817b7-114">Application Insights recopila datos automáticamente sobre las tasas de respuesta, las tasas de error, los tiempos de respuesta de dependencia, etc.</span><span class="sxs-lookup"><span data-stu-id="817b7-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="817b7-115">Application Insights admite el registro de eventos y métricas personalizados específicos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="817b7-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="817b7-116">Aplicación de Azure Insights proporciona varias maneras de proporcionar información sobre las aplicaciones supervisadas:</span><span class="sxs-lookup"><span data-stu-id="817b7-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="817b7-117">[Mapa de aplicación](/azure/application-insights/app-insights-app-map) : ayuda a identificar los cuellos de botella de rendimiento o las zonas activas de errores en todos los componentes de las aplicaciones distribuidas.</span><span class="sxs-lookup"><span data-stu-id="817b7-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="817b7-118">[Azure explorador de métricas](/azure/azure-monitor/platform/metrics-getting-started) es un componente del Microsoft Azure portal que permite trazar gráficos, correlacionar visualmente las tendencias e investigar picos y caídas en los valores de las métricas.</span><span class="sxs-lookup"><span data-stu-id="817b7-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="817b7-119">[Hoja rendimiento en el portal de Application Insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="817b7-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="817b7-120">Muestra detalles de rendimiento para distintas operaciones en la aplicación supervisada.</span><span class="sxs-lookup"><span data-stu-id="817b7-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="817b7-121">Permite profundizar en una sola operación para comprobar todas las partes o dependencias que contribuyen a una larga duración.</span><span class="sxs-lookup"><span data-stu-id="817b7-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="817b7-122">Profiler se puede invocar desde aquí para recopilar seguimientos de rendimiento a petición.</span><span class="sxs-lookup"><span data-stu-id="817b7-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="817b7-123">El [generador de perfiles de aplicación de Azure Insights](/azure/azure-monitor/app/profiler) permite la generación de perfiles regulares y a petición de aplicaciones .net.</span><span class="sxs-lookup"><span data-stu-id="817b7-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="817b7-124">Azure Portal muestra los seguimientos de rendimiento capturados con pilas de llamadas y rutas de acceso activas.</span><span class="sxs-lookup"><span data-stu-id="817b7-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="817b7-125">Los archivos de seguimiento también se pueden descargar para realizar análisis más profundos con PerfView.</span><span class="sxs-lookup"><span data-stu-id="817b7-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="817b7-126">Application Insights puede usarse en entornos de variedad:</span><span class="sxs-lookup"><span data-stu-id="817b7-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="817b7-127">Optimizado para funcionar en Azure.</span><span class="sxs-lookup"><span data-stu-id="817b7-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="817b7-128">Funciona en producción, desarrollo y almacenamiento provisional.</span><span class="sxs-lookup"><span data-stu-id="817b7-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="817b7-129">Funciona localmente desde [Visual Studio](/azure/application-insights/app-insights-visual-studio) o en otros entornos de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="817b7-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="817b7-130">Para más información, consulte [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="817b7-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="817b7-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="817b7-131">PerfView</span></span>

<span data-ttu-id="817b7-132">[PerfView](https://github.com/Microsoft/perfview) es una herramienta de análisis de rendimiento creada por el equipo de .net específicamente para diagnosticar problemas de rendimiento de .net.</span><span class="sxs-lookup"><span data-stu-id="817b7-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="817b7-133">PerfView permite el análisis del uso de la CPU, el comportamiento de la memoria y del GC, los eventos de rendimiento y el tiempo de reloj.</span><span class="sxs-lookup"><span data-stu-id="817b7-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="817b7-134">Puede obtener más información sobre PerfView y cómo empezar a usar los [tutoriales de vídeo de PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) o leer la guía del usuario disponible en la herramienta o [en github](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="817b7-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="817b7-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="817b7-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="817b7-136">El [Kit de herramientas de rendimiento de Windows](/windows-hardware/test/wpt/) (WPT) consta de dos componentes: Windows performance Recorder (WPR) y Windows Performance Analyzer (WPA).</span><span class="sxs-lookup"><span data-stu-id="817b7-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="817b7-137">Las herramientas de generan perfiles de rendimiento detallados de las aplicaciones y los sistemas operativos Windows.</span><span class="sxs-lookup"><span data-stu-id="817b7-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="817b7-138">WPT tiene maneras más completas de visualizar datos, pero su recopilación de datos es menos eficaz que la de PerfView.</span><span class="sxs-lookup"><span data-stu-id="817b7-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="817b7-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="817b7-139">PerfCollect</span></span>

<span data-ttu-id="817b7-140">Aunque PerfView es una herramienta de análisis de rendimiento útil para escenarios de .NET, solo se ejecuta en Windows, por lo que no se puede usar para recopilar seguimientos de ASP.NET Core aplicaciones que se ejecutan en entornos de Linux.</span><span class="sxs-lookup"><span data-stu-id="817b7-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="817b7-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) es un script de bash que usa las herramientas de generación de perfiles de Linux nativas ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) y [LTTng](https://lttng.org/)) para recopilar seguimientos en Linux que PerfView puede analizar.</span><span class="sxs-lookup"><span data-stu-id="817b7-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="817b7-142">PerfCollect es útil cuando los problemas de rendimiento se muestran en entornos de Linux donde PerfView no se puede usar directamente.</span><span class="sxs-lookup"><span data-stu-id="817b7-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="817b7-143">En su lugar, PerfCollect puede recopilar seguimientos de aplicaciones de .NET Core que, a continuación, se analizan en un equipo Windows mediante PerfView.</span><span class="sxs-lookup"><span data-stu-id="817b7-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="817b7-144">Puede encontrar más información sobre cómo instalar y empezar a trabajar con PerfCollect [en github](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="817b7-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="817b7-145">Otras herramientas de rendimiento de terceros</span><span class="sxs-lookup"><span data-stu-id="817b7-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="817b7-146">A continuación se enumeran algunas herramientas de rendimiento de terceros que son útiles para la investigación de rendimiento de aplicaciones .NET Core.</span><span class="sxs-lookup"><span data-stu-id="817b7-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="817b7-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="817b7-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="817b7-148">[dotTrace](https://www.jetbrains.com/profiler/) y [dotMemory](https://www.jetbrains.com/dotmemory/) de [JetBrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="817b7-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="817b7-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) de Intel</span><span class="sxs-lookup"><span data-stu-id="817b7-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
