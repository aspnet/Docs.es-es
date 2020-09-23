---
title: Por qué migrar WCF a gRPC de ASP.NET Core
author: markrendle
description: En este artículo se ofrece un resumen de por qué el servicio gRPC de ASP.NET Core es una buena opción para los desarrolladores de Windows Communication Foundation (WCF) que quieren migrar a arquitecturas y plataformas modernas.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 811e6037b058b26fcf91063123d04d448a9a28a8
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2020
ms.locfileid: "90012749"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC para desarrolladores de Windows Communication Foundation (WCF)

En este artículo se ofrece un resumen de por qué el servicio gRPC de ASP.NET Core es una buena opción para los desarrolladores de Windows Communication Foundation (WCF) que quieren migrar a arquitecturas y plataformas modernas.

## <a name="comparison-to-wcf"></a>Comparación con WCF

Aunque la implementación y el enfoque son diferentes para gRPC, la experiencia de desarrollo y consumo de servicios con gRPC debe ser intuitiva para los desarrolladores de WCF. WCF y gRPC son marcos de RPC (llamada a procedimiento remoto) con los mismos objetivos:

* Hacer posible el código como si el cliente y el servidor estuvieran en la misma plataforma.
* Ofrecer una API de redes portátiles simplificada.

Ambas plataformas comparten el requisito de declarar e implementar una interfaz, aunque el proceso para declarar la interfaz es diferente. Los muchos tipos de llamadas RPC que gRPC admite se asignan bien a los enlaces disponibles en servicios WCF. Para más información y ejemplos, consulte [Migración de una solución WCF a gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Ventajas de gRPC

gRPC ofrece un marco mejor que otros enfoques por los siguientes motivos.

### <a name="performance"></a>Rendimiento

gRPC usa HTTP/2. A diferencia de HTTP/1.1, HTTP/2:

* Es un protocolo binario más pequeño y más rápido.
* Es más eficaz para que los equipos lo analicen.
* Admite solicitudes de multiplexación a través de una única conexión. La multiplexación permite que se envíen varias solicitudes a través de una conexión sin que las solicitudes se bloqueen entre sí. En HTTP/1.1, el bloqueo se conoce como "bloqueo de encabezado de línea (HOL)".

gRPC usa Protobuf, un formato binario y eficaz, para serializar mensajes. Los mensajes de Protobuf:
* Son rápidos para serializar y deserializar.
* Usan menos ancho de banda que los formatos basados en texto. 

gRPC es una buena solución para dispositivos móviles y redes con restricciones de ancho de banda.

### <a name="interoperability"></a>Interoperabilidad

Hay herramientas y bibliotecas de gRPC para todos los lenguajes de programación y plataformas principales, como .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby y PHP. Gracias al formato de conexión binaria de Protobuf y la generación de código eficaz para cada plataforma, los desarrolladores pueden crear aplicaciones de rendimiento multiplataforma.

### <a name="usability-and-productivity"></a>Facilidad de uso y productividad

gRPC es una solución completa de RPC. Funciona de forma coherente en varios lenguajes y plataformas. También ofrece excelentes herramientas, con gran parte del código reutilizable que se genera automáticamente. Como WCF, gRPC genera automáticamente mensajes y un cliente fuertemente tipado. Se libera tiempo al desarrollador para que se centre en la lógica empresarial.

### <a name="streaming"></a>Streaming

gRPC tiene un streaming bidireccional completo, que proporciona una funcionalidad similar a los servicios dúplex completo de WCF. La transmisión por secuencias de gRPC puede funcionar a través de conexiones de Internet normales, equilibradores de carga y mallas de servicio.

### <a name="deadlines-timeouts-and-cancellation"></a>Fechas límite, tiempos de expiración y cancelación

gRPC permite a los clientes especificar el tiempo máximo en que un RPC debe finalizar. Si se supera la fecha límite especificada, el servidor puede cancelar la operación independientemente del cliente. Las fechas límite y cancelaciones se pueden propagar a través de las llamadas subsiguientes de gRPC para ayudar a aplicar los límites de uso de recursos. Los clientes pueden detener las operaciones cuando se supera una fecha límite, o antes, si es necesario. Por ejemplo, los clientes pueden detener las operaciones debido a una interacción del usuario.

### <a name="security"></a>Seguridad

gRPC puede usar TLS y HTTP/2 para proporcionar una conexión cifrada de un extremo a otro entre el cliente y el servidor. La compatibilidad con la autenticación de certificados de cliente aumenta aún más la seguridad y la confianza entre el cliente y el servidor.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC como ruta de acceso de migración de WCF a .NET Core y .NET 5

.NET Core y .NET 5 marcan un cambio en la forma en que Microsoft ofrece soluciones de comunicación remota a los desarrolladores que quieren ofrecer servicios a través de una variedad de plataformas. .NET Core y .NET 5 admiten [llamadas a servicios WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), pero no ofrecen compatibilidad del lado servidor para hospedar WCF.

Hay dos rutas de acceso recomendadas para modernizar aplicaciones WCF:

* gRPC se basa en tecnologías modernas y ha surgido como la opción más popular en la comunidad de desarrolladores para aplicaciones RPC. A partir de .NET Core 3.0, las plataformas .NET modernas tienen una excelente compatibilidad con gRPC. La migración de servicios WCF para usar gRPC ayuda a proporcionar las características de RPC, el rendimiento, una interoperabilidad necesaria en las aplicaciones modernas.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) es un esfuerzo de la comunidad por ofrecer compatibilidad con el hospedaje de servicios WCF en .NET Core y .NET 5. Hay disponible una versión preliminar y el proyecto avanza para estar preparado para la producción. CoreWCF solo admite un subconjunto de características de WCF y las aplicaciones de .NET Framework que se migran para usarlas necesitarán cambios en el código y pruebas para que se realicen correctamente. CoreWCF es una buena opción si una aplicación tiene que mantener la compatibilidad con los clientes existentes que llaman a servicios WCF.

## <a name="get-started"></a>Introducción

Para obtener instrucciones detalladas sobre cómo crear servicios gRPC en ASP.NET Core para desarrolladores de WCF, consulte [gRPC de ASP.NET Core para desarrolladores de WCF](/dotnet/architecture/grpc-for-wcf-developers).
