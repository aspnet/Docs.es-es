---
title: Clientes y servicios gRPC mediante Code First con .NET
author: jamesnk
description: Conozca los conceptos básicos a la hora de escribir gRPC mediante Code First con .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880624"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>Clientes y servicios gRPC mediante Code First con .NET

Por [James Newton-King](https://twitter.com/jamesnk) y [Marc Gravell](https://twitter.com/marcgravell)

gRPC mediante Code First usa tipos .NET para definir contratos de servicio y de mensajes.

Code First es una buena opción cuando un sistema completo usa .NET:

* El servidor y los clientes de .NET pueden compartir los tipos de contratos de datos y servicio de .NET.
* Esto evita la necesidad de definir contratos en la generación de código y los archivos `.proto`.

Code First no se recomienda en sistemas políglotas con varios idiomas. Los tipos de contratos de datos y servicio de .NET no se pueden usar con plataformas que no son de .NET. Para llamar a un servicio gRPC escrito mediante Code First, otras plataformas deben crear un contrato de `.proto` que coincida con el servicio.

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> Para obtener ayuda con protobuf-net.Grpc, visite el [sitio web de protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) o cree una incidencia en el [repositorio de GitHub de protobuf-net.Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) es un proyecto de la comunidad y no es compatible con Microsoft. Agrega compatibilidad con Code First a `Grpc.AspNetCore` y `Grpc.Net.Client`. Usa tipos de .NET anotados con atributos para definir los mensajes y servicios de gRPC de una aplicación.

El primer paso para crear un servicio gRPC mediante Code First es definir el contrato de código:

* Cree un proyecto que compartirán el servidor y el cliente.
* Agregue una referencia al paquete [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).
* Cree tipos de contratos de datos y de servicio.

[!code-csharp[](code-first/Contracts.cs)]

El código anterior:

* Define los mensajes `HelloRequest` y `HelloReply`.
* Define la interfaz del contrato de `IGreeterService` con el método de gRPC `SayHelloAsync` unario.

El contrato de servicio se implementa en el servidor y se invoca desde el cliente. Los métodos definidos en las interfaces de servicio deben coincidir con determinadas firmas, en función de cómo sean, es decir, unarios, streaming de servidor, streaming de cliente o streaming bidireccional.

Para más información sobre la definición de contratos de servicio, vea la [documentación de introducción de protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Creación de un servicio gRPC mediante Code First

Para agregar un servicio gRPC mediante Code First a una aplicación de ASP.NET Core:

* Agregue una referencia al paquete [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore).
* Agregue una referencia al proyecto de contrato de código compartido.

Cree un archivo `GreeterService.cs` e implemente la interfaz de servicio de `IGreeterService`:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Actualice el archivo `Startup.cs`:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

En el código anterior:

* `AddCodeFirstGrpc` registra servicios que habilitan Code First.
* `MapGrpcService<GreeterService>` agrega el punto de conexión de servicio de Code First.

Los servicios gRPC implementados con Code First y archivos `.proto` pueden coexistir en la misma aplicación. Todos los servicios gRPC usan la [configuración del servicio gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Creación de un cliente de gRPC mediante Code First

Un cliente de gRPC mediante Code First usa el contrato de servicio para llamar a los servicios gRPC. Para llamar a un servicio gRPC mediante un cliente Code First:

* Agregue una referencia al paquete [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).
* Agregue una referencia al proyecto de contrato de código compartido.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

El código anterior:

* Crea un canal gRPC.
* Crea un cliente Code First a partir del canal con el método de extensión `CreateGrpcService<IGreeterService>`.
* Llama al servicio gRPC con `SayHelloAsync`.

Un cliente gRPC mediante Code First se crea a partir de un canal. Al igual que un cliente normal, un cliente Code First usa su [configuración de canal](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Recursos adicionales

* [Sitio web de protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [Repositorio de GitHub de protobuf-net.Grpc](https://github.com/protobuf-net/protobuf-net.Grpc)
