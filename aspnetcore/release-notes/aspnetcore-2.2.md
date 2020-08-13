---
title: Novedades de ASP.NET Core 2.2
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 2.2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.2
ms.openlocfilehash: 354839abe295b4adcbf6becfd3bd926330f0823f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021151"
---
# <a name="whats-new-in-aspnet-core-22"></a>Novedades de ASP.NET Core 2.2

En este artículo se resaltan los cambios más importantes de ASP.NET Core 2.2, con vínculos a la documentación pertinente.

## <a name="openapi-analyzers--conventions"></a>Convenciones y analizadores de OpenAPI

OpenAPI (antes conocido como Swagger) es una especificación independiente del lenguaje que sirve para describir API REST. El ecosistema de OpenAPI dispone de herramientas que permiten descubrir, probar y generar código de cliente mediante la especificación. El soporte técnico para generar y visualizar los documentos de OpenAPI en ASP.NET Core MVC se proporciona a través de proyectos controlados por la comunidad como [NSwag](https://github.com/RicoSuter/NSwag) y [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2.2 proporciona experiencias de uso de herramientas y entornos de ejecución mejoradas para crear documentos de OpenAPI.

Para obtener más información, vea los siguientes recursos:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: Convenciones y analizadores de OpenAPI](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Soporte técnico para los detalles del problema

ASP.NET Core 2.1 introdujo `ProblemDetails`, según la especificación [RFC 7807](https://tools.ietf.org/html/rfc7807), para comunicar los detalles de un error con una respuesta HTTP. En 2.2, `ProblemDetails` es la respuesta estándar para los códigos de error de cliente en los controladores con el atributo `ApiControllerAttribute`. Un elemento `IActionResult` que anteriormente devolvía un código de estado de error de cliente (4xx) ahora devuelve un cuerpo `ProblemDetails`. El resultado también incluye un identificador de correlación que se puede usar para correlacionar el error mediante los registros de solicitudes. En el caso de los errores de cliente, el procedimiento predeterminado de `ProducesResponseType` es utilizar `ProblemDetails` como tipo de respuesta. Esto se documenta en los resultados de OpenAPI/Swagger que se generan mediante NSwag o Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Enrutamiento de punto de conexión

ASP.NET Core 2.2 usa un nuevo sistema de *enrutamiento de punto de conexión* para mejorar la distribución de las solicitudes. Los cambios incluyen nuevos miembros de API de generación de vínculo y transformadores de parámetro de ruta.

Para obtener más información, vea los siguientes recursos:

* [Enrutamiento de punto de conexión en la versión 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformadores de parámetro de ruta](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (consultar la sección **Enrutamiento**)
* [Diferencias entre el enrutamiento basado en IRouter y en el punto de conexión](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Comprobaciones de estado

Un nuevo servicio de comprobaciones de estado facilita el uso de ASP.NET Core en entornos que requieren comprobaciones de estado, como Kubernetes. Las comprobaciones de estado incluyen middleware y un conjunto de bibliotecas que definen una abstracción y un servicio de `IHealthCheck`.

Un orquestador de contenedores o un equilibrador de carga utilizan las comprobaciones de estado para determinar rápidamente si un sistema está respondiendo correctamente a las solicitudes. Para responder a una comprobación de estado con errores, es posible que un orquestador de contenedores detenga una implementación en curso o reinicie un contenedor. Para responder a una comprobación de estado, es posible que un equilibrador de carga enrute el tráfico al margen de la instancia con errores del servicio.

Una aplicación expone las comprobaciones de estado como un punto de conexión HTTP que los sistemas de supervisión utilizan. Las comprobaciones de estado pueden configurarse para diversos escenarios y sistemas de supervisión en tiempo real. El servicio de comprobaciones de estado se integra con el [proyecto BeatPulse](https://github.com/Xabaril/BeatPulse), lo que facilita agregar comprobaciones de docenas de sistemas y dependencias conocidos.

Para obtener más información, consulte [Comprobaciones de estado en ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 en Kestrel

ASP.NET Core 2.2 es compatible con HTTP/2.

HTTP/2 es una revisión completa del protocolo HTTP. Las características destacadas de HTTP/2 incluyen:

* Compatibilidad con la compresión de encabezados.
* Secuencias totalmente multiplexadas en una sola conexión.

Aunque HTTP/2 conserva la semántica de HTTP (por ejemplo, encabezados y métodos HTTP), la manera de entramar y enviar los datos entre el cliente y el servidor es una diferencia importante respecto a HTTP/1.x.

Como consecuencia de este cambio en las tramas, los servidores y los clientes deben negociar la versión del protocolo que se va a utilizar. La negociación de protocolo de capa de aplicación (ALPN) es una extensión TLS que permite que el servidor y el cliente negocien la versión del protocolo que se va a utilizar como parte de su protocolo de enlace TLS. Aunque es posible que el servidor y el cliente conozcan previamente el protocolo, los principales exploradores admiten ALPN como la única forma de establecer una conexión HTTP/2.

Para obtener más información, consulte [Compatibilidad con HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Configuración de Kestrel

En versiones anteriores de ASP.NET Core, las opciones de Kestrel se configuran mediante una llamada a `UseKestrel`. En la versión 2.2, las opciones de Kestrel se configuran mediante una llamada a `ConfigureKestrel` en el generador de host. Este cambio resuelve un problema con el orden de los registros de `IServer` para el hospedaje en proceso. Para obtener más información, vea los siguientes recursos:

* [Mitigación de conflictos de UseIIS](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Configuración de las opciones del servidor Kestrel con ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hospedaje en proceso de IIS

En versiones anteriores de ASP.NET Core, IIS actuaba como un proxy inverso. En la versión 2.2, el módulo ASP.NET Core puede arrancar el CoreCLR y hospedar una aplicación dentro del proceso de trabajo de IIS (*w3wp.exe*). El hospedaje en proceso proporciona mejoras de rendimiento y diagnóstico cuando se ejecuta con IIS.

Para obtener más información, consulte [Modelo de hospedaje en proceso](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="no-locsignalr-java-client"></a>Cliente de Java para SignalR

ASP.NET Core 2.2 presenta un cliente de Java para SignalR. Este cliente admite la conexión a una instancia de ASP.NET Core SignalR Server desde código de Java, incluidas las aplicaciones Android.

Para más información, consulte [Cliente de Java de ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Mejoras de CORS

En versiones anteriores de ASP.NET Core, CORS Middleware permitía que los encabezados `Accept`, `Accept-Language`, `Content-Language` y `Origin` se enviaran independientemente de los valores configurados en `CorsPolicy.Headers`. En la versión 2.2, cumplir la directiva de CORS Middleware solo es posible cuando los encabezados enviados en `Access-Control-Request-Headers` coinciden exactamente con los indicados en `WithHeaders`.

Para obtener más información consulte [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Compresión de las respuestas

ASP.NET Core 2.2 puede comprimir las respuestas con el [formato de compresión Brotli](https://tools.ietf.org/html/rfc7932).

Para obtener más información, consulte [Compresión de respuesta en ASP.NET Core](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Plantillas de proyecto

Las plantillas de proyecto web de ASP.NET Core se han actualizado a [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) y [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). La nueva apariencia es más sencilla y permite ver con más facilidad las estructuras importantes de la aplicación.

![Página Inicio o Índice](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Rendimiento de la validación

El sistema de validación de MVC está diseñado para ser extensible y flexible, lo que permite determinar en función de la solicitud qué validadores se aplican a un modelo determinado. Esto es muy útil para crear proveedores de validación compleja. Sin embargo, por lo general, una aplicación solo usa los validadores integrados y no requiere esta flexibilidad adicional. Los validadores integrados incluyen DataAnnotations como [Required], [StringLength] y `IValidatableObject`.

En ASP.NET Core 2.2, MVC puede cortocircuitar la validación si determina que un gráfico de modelo determinado no requiere validación. Al validar modelos que no pueden tener o no tienen validadores, se producen mejoras significativas si se omite la validación. Esto incluye objetos como colecciones de primitivos (como `byte[]`, `string[]` o `Dictionary<string, string>`) o gráficos de objetos complejos sin muchos validadores.

## <a name="http-client-performance"></a>Rendimiento del cliente HTTP

En ASP.NET Core 2.2, para mejorar el rendimiento de `SocketsHttpHandler`, se ha reducido la contención del bloqueo de grupo de conexiones. Se ha mejorado el rendimiento para las aplicaciones que realizan muchas solicitudes HTTP salientes, por ejemplo, algunas arquitecturas de microservicios. Bajo una carga, el rendimiento de `HttpClient` se puede mejorar hasta en un 60 % en Linux y en un 20 % en Windows.

Para obtener más información, consulte la [solicitud de incorporación de cambios que propició esta mejora](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Información adicional

Para ver la lista completa de cambios, consulte las [Notas de la versión de ASP.NET Core 2.2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).
