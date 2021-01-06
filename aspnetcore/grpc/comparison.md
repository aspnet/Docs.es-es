---
title: Comparación entre los servicios gRPC y las API HTTP
author: jamesnk
description: Obtenga información sobre cómo se compara gRPC con las API de HTTP y cuáles son los escenarios recomendados.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: 0fb50f07153f5f9953b667fe32062ad24b2bd66d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059954"
---
# <a name="compare-grpc-services-with-http-apis"></a>Comparación entre los servicios gRPC y las API HTTP

Por [James Newton-King](https://twitter.com/jamesnk)

En este artículo se realiza una comparación entre los [servicios gRPC](https://grpc.io/docs/guides/) y las API HTTP con JSON (incluidas las [API web](xref:web-api/index) de ASP.NET Core). La tecnología que se usa para proporcionar una API para la aplicación es una decisión importante, y gRPC ofrece ventajas exclusivas en comparación con las API HTTP. En este artículo se describen las ventajas y los inconvenientes de gRPC, y se recomiendan escenarios para usar gRPC antes que otras tecnologías.

## <a name="high-level-comparison"></a>Comparación general

En la tabla siguiente se ofrece una comparación general de las características entre gRPC y las API HTTP con JSON.

| Característica          | gRPC                                               | API HTTP con JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contrato         | Obligatorio ( *.proto*)                                | Opcional (OpenAPI)            |
| Protocolo         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (pequeño, binario)](#performance)           | JSON (grande, legible para usuarios)  |
| Carácter preceptivo | [Especificación estricta](#strict-specification)      | Flexible. Cualquier HTTP es válido.     |
| Streaming        | [Cliente, servidor, bidireccional](#streaming)       | Cliente, servidor                |
| Compatibilidad con exploradores  | [No (requiere grpc-web)](#limited-browser-support) | Sí                           |
| Seguridad         | Transporte (TLS)                                    | Transporte (TLS)               |
| Generación de código de cliente | [Sí](#code-generation)                      | OpenAPI + herramientas de terceros |

## <a name="grpc-strengths"></a>Ventajas de gRPC

### <a name="performance"></a>Rendimiento

Los mensajes de gRPC se serializan mediante [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), un formato de mensaje binario eficaz. Protobuf se serializa muy rápidamente en el servidor y el cliente. La serialización de Protobuf genera cargas de mensajes pequeñas, un aspecto importante en escenarios de ancho de banda limitado como las aplicaciones móviles.

gRPC está diseñado para HTTP/2, una revisión principal de HTTP que proporciona importantes ventajas de rendimiento con respecto a HTTP 1.x:

* Tramas binarias y compresión. El protocolo HTTP/2 es compacto y eficaz tanto para el envío como para la recepción.
* Multiplexación de varias llamadas HTTP/2 a través de una única conexión TCP. La multiplexación elimina el [bloqueo de encabezado de línea](https://en.wikipedia.org/wiki/Head-of-line_blocking).

HTTP/2 no es exclusivo de gRPC. Muchos tipos de solicitud, incluidas las API HTTP con JSON, pueden usar HTTP/2 y beneficiarse de sus mejoras de rendimiento.

### <a name="code-generation"></a>Generación de código

Todos los marcos de trabajo de gRPC proporcionan compatibilidad de primera clase con la generación de código. Un archivo principal para el desarrollo de gRPC es [.proto](https://developers.google.com/protocol-buffers/docs/proto3), en el que se define el contrato de servicios y mensajes de gRPC. A partir de este archivo, los marcos gRPC generarán el código de una clase base de servicio, mensajes y un cliente completo.

Mediante el uso compartido del archivo *.proto* entre el servidor y el cliente, los mensajes y el código de cliente se pueden generar de extremo a extremo. La generación de código del cliente elimina la duplicación de mensajes en el cliente y el servidor, y crea de forma automática un cliente fuertemente tipado. Al no tener que escribir un cliente se ahorra considerablemente tiempo de desarrollo en las aplicaciones con muchos servicios.

### <a name="strict-specification"></a>Especificación estricta

No existe una especificación formal para la API HTTP con JSON. Los desarrolladores debaten el mejor formato de las direcciones URL, los verbos HTTP y los códigos de respuesta.

La [especificación gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) es preceptiva con respecto al formato que debe seguir un servicio gRPC. gRPC elimina el debate y ahorra tiempo de desarrollo, ya que es coherente entre las plataformas y las implementaciones.

### <a name="streaming"></a>Streaming

HTTP/2 proporciona una base para las secuencias de comunicación en tiempo real de larga duración. gRPC proporciona compatibilidad de primera clase para el streaming a través de HTTP/2.

Un servicio gRPC admite todas las combinaciones de streaming:

* Unario (sin streaming)
* Streaming del servidor al cliente
* Streaming del cliente al servidor
* Streaming bidireccional

### <a name="deadlinetimeouts-and-cancellation"></a>Fecha límite o tiempos de expiración y cancelación

gRPC permite a los clientes especificar cuánto tiempo están dispuestos a esperar a que se complete una RPC. La [fecha límite](https://grpc.io/blog/deadlines) se envía al servidor y este puede decidir qué acción realizar si supera la fecha límite. Por ejemplo, es posible que el servidor cancele las solicitudes de gRPC, HTTP o base de datos en curso si se alcanza el tiempo de expiración.

La propagación de la fecha límite y la cancelación mediante llamadas secundarias de gRPC ayuda a aplicar los límites de uso de recursos.

## <a name="grpc-recommended-scenarios"></a>Escenarios recomendados de gRPC

gRPC se adapta perfectamente a los escenarios siguientes:

* **Microservicios**: gRPC está diseñado para la comunicación de baja latencia y rendimiento alto. gRPC resulta idóneo para microservicios ligeros en los que la eficiencia sea crítica.
* **Comunicación punto a punto en tiempo real**: gRPC tiene una excelente compatibilidad con el streaming bidireccional. Los servicios gRPC pueden insertar mensajes en tiempo real sin sondeos.
* **Entornos políglotas**: las herramientas de gRPC admiten todos los lenguajes de desarrollo más populares, lo que convierte a gRPC en una buena opción para entornos de varios lenguajes.
* **Entornos restringidos de red**: los mensajes gRPC se serializan con Protobuf, un formato de mensaje ligero. Un mensaje gRPC siempre es más pequeño que un mensaje JSON equivalente.
* **(IPC)** : los transportes de IPC, como los sockets de dominio de Unix y las canalizaciones con nombre, se pueden usar con gRPC para la comunicación entre aplicaciones en el mismo equipo. Para obtener más información, vea <xref:grpc/interprocess>.

## <a name="grpc-weaknesses"></a>Inconvenientes de gRPC

### <a name="limited-browser-support"></a>Compatibilidad limitada con exploradores

En la actualidad no es posible llamar directamente a un servicio gRPC desde un explorador. gRPC usa intensamente características de HTTP/2 y ningún explorador proporciona el nivel de control requerido a través de solicitudes web para admitir un cliente de gRPC. Por ejemplo, los exploradores no permiten que el autor de la llamada exija el uso de HTTP/2, ni proporcionan acceso a los marcos HTTP/2 subyacentes.

Los métodos comunes para llevar gRPC a las aplicaciones de explorador son dos:

* [gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) es una tecnología adicional del equipo de gRPC que proporciona compatibilidad con gRPC en el explorador. gRPC-Web permite que las aplicaciones de explorador saquen partido del uso de red de alto rendimiento y bajo nivel de gRPC. No todas las características de gRPC son compatibles con gRPC-Web. No se admite el streaming de cliente y bidireccional, y existe compatibilidad limitada para el streaming de servidor.

  .NET Core es compatible con gRPC-Web. Para obtener más información, vea <xref:grpc/browser>.

* Se pueden crear API web de JSON de RESTful automáticamente desde servicios gRPC, anotando para ello el archivo *.proto* con [metadatos HTTP](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule). Esto permite que una aplicación admita API web de JSON y gRPC, sin duplicar el esfuerzo de tener que crear servicios independientes para cada uno de ellos.

  .NET Core tiene compatibilidad experimental para crear API web de JSON desde servicios gRPC. Para obtener más información, vea <xref:grpc/httpapi>.

### <a name="not-human-readable"></a>No es legible por el usuario

Las solicitudes de API HTTP se envían como texto y se pueden leer y crear por parte de los usuarios.

De forma predeterminada, los mensajes gRPC se codifican con Protobuf. Aunque Protobuf es eficaz para el envío y la recepción, su formato binario no es legible. Protobuf requiere la descripción de la interfaz del mensaje especificada en el archivo *.proto* para deserializarse correctamente. Se requieren herramientas adicionales para analizar las cargas de Protobuf en la conexión y redactar las solicitudes a mano.

Existen características como la [reflexión del servidor](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) y la [herramienta de línea de comandos de gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) para ayudar con los mensajes de Protobuf binarios. Además, los mensajes de Protobuf admiten [la conversión a y desde JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). La conversión de JSON integrada proporciona una manera eficaz de convertir los mensajes de Protobuf a y desde formato legible durante la depuración.

## <a name="alternative-framework-scenarios"></a>Escenarios de marcos alternativos

En los escenarios siguientes se recomiendan otros marcos de trabajo antes que gRPC:

* **API accesibles de explorador**: gRPC no se admite de forma completa en el explorador. gRPC-Web puede ofrecer compatibilidad con el explorador, pero tiene limitaciones e introduce un proxy de servidor.
* **Retransmisión de la comunicación en tiempo real**: gRPC admite la comunicación en tiempo real a través de streaming, pero no existe el concepto de retransmisión de un mensaje a las conexiones registradas. Por ejemplo, en un escenario de salón de chat en el que se deben enviar nuevos mensajes de chat a todos los clientes, es necesario que cada llamada gRPC transmita de forma individual los nuevos mensajes de chat al cliente. [SignalR](xref:signalr/introduction) es un marco útil para este escenario. SignalR tiene el concepto de conexiones persistentes y compatibilidad integrada para la retransmisión de mensajes.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
