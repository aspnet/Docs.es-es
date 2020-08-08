---
title: Herramientas de diagnóstico de rendimiento
author: mjrousos
description: Herramientas útiles para diagnosticar problemas de rendimiento en aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 53c613b208c142e1323e593d0c57a42de3150621
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018071"
---
# <a name="performance-diagnostic-tools"></a>Herramientas de diagnóstico de rendimiento

Por [Mike Rousos](https://github.com/mjrousos)

En este artículo se enumeran las herramientas para diagnosticar problemas de rendimiento en ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Herramientas de diagnóstico de Visual Studio

Las [herramientas de generación de perfiles y diagnóstico](/visualstudio/profiling) integradas en Visual Studio son un buen lugar para empezar a investigar los problemas de rendimiento. Estas herramientas son eficaces y prácticas de uso desde el entorno de desarrollo de Visual Studio. Las herramientas permiten el análisis del uso de la CPU, el uso de la memoria y los eventos de rendimiento en ASP.NET Core aplicaciones. La generación de perfiles facilita la generación de perfiles en el tiempo de desarrollo.

Puede encontrar más información en la [documentación de Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) proporciona datos de rendimiento detallados para la aplicación. Application Insights recopila datos automáticamente sobre las tasas de respuesta, las tasas de error, los tiempos de respuesta de dependencia, etc. Application Insights admite el registro de eventos y métricas personalizados específicos de la aplicación.

Aplicación de Azure Insights proporciona varias maneras de proporcionar información sobre las aplicaciones supervisadas:

- [Mapa de aplicación](/azure/application-insights/app-insights-app-map) : ayuda a identificar los cuellos de botella de rendimiento o las zonas activas de errores en todos los componentes de las aplicaciones distribuidas.
- [Azure explorador de métricas](/azure/azure-monitor/platform/metrics-getting-started) es un componente del Microsoft Azure portal que permite trazar gráficos, correlacionar visualmente las tendencias e investigar picos y caídas en los valores de las métricas.
- [Hoja rendimiento en el portal de Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Muestra detalles de rendimiento para distintas operaciones en la aplicación supervisada.
  - Permite profundizar en una sola operación para comprobar todas las partes o dependencias que contribuyen a una larga duración.
  - Profiler se puede invocar desde aquí para recopilar seguimientos de rendimiento a petición.

- El [generador de perfiles de aplicación de Azure Insights](/azure/azure-monitor/app/profiler) permite la generación de perfiles regulares y a petición de aplicaciones .net.  Azure Portal muestra los seguimientos de rendimiento capturados con pilas de llamadas y rutas de acceso activas. Los archivos de seguimiento también se pueden descargar para realizar análisis más profundos con PerfView.

Application Insights puede usarse en entornos de variedad:

- Optimizado para funcionar en Azure.
- Funciona en producción, desarrollo y almacenamiento provisional.
- Funciona localmente desde [Visual Studio](/azure/application-insights/app-insights-visual-studio) o en otros entornos de hospedaje.

Para más información, consulte [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) es una herramienta de análisis de rendimiento creada por el equipo de .net específicamente para diagnosticar problemas de rendimiento de .net. PerfView permite el análisis del uso de la CPU, el comportamiento de la memoria y del GC, los eventos de rendimiento y el tiempo de reloj.

Puede obtener más información sobre PerfView y cómo empezar a usar los [tutoriales de vídeo de PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) o leer la guía del usuario disponible en la herramienta o [en github](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

El [Kit de herramientas de rendimiento de Windows](/windows-hardware/test/wpt/) (WPT) consta de dos componentes: Windows performance Recorder (WPR) y Windows Performance Analyzer (WPA). Las herramientas de generan perfiles de rendimiento detallados de las aplicaciones y los sistemas operativos Windows. WPT tiene maneras más completas de visualizar datos, pero su recopilación de datos es menos eficaz que la de PerfView.

## <a name="perfcollect"></a>PerfCollect

Aunque PerfView es una herramienta de análisis de rendimiento útil para escenarios de .NET, solo se ejecuta en Windows, por lo que no se puede usar para recopilar seguimientos de ASP.NET Core aplicaciones que se ejecutan en entornos de Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) es un script de bash que usa las herramientas de generación de perfiles de Linux nativas ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) y [LTTng](https://lttng.org/)) para recopilar seguimientos en Linux que PerfView puede analizar. PerfCollect es útil cuando los problemas de rendimiento se muestran en entornos de Linux donde PerfView no se puede usar directamente. En su lugar, PerfCollect puede recopilar seguimientos de aplicaciones de .NET Core que, a continuación, se analizan en un equipo Windows mediante PerfView.

Puede encontrar más información sobre cómo instalar y empezar a trabajar con PerfCollect [en github](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Otras herramientas de rendimiento de terceros

A continuación se enumeran algunas herramientas de rendimiento de terceros que son útiles para la investigación de rendimiento de aplicaciones .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace y dotMemory de JetBrains
- VTune de Intel
