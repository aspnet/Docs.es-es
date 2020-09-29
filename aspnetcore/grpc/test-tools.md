---
title: Comprobación de servicios gRPC con gRPCurl en ASP.NET Core
author: jamesnk
description: Aprenda a probar servicios con las herramientas de gRPC. gRPCurl una herramienta de línea de comandos para interactuar con los servicios de gRPC. gRPCui es una interfaz de usuario web interactiva.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770173"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Comprobación de servicios gRPC con gRPCurl en ASP.NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

Hay herramientas disponibles para gRPC que permiten a los desarrolladores probar servicios sin necesidad de compilar aplicaciones cliente:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) es una herramienta de línea de comandos que proporciona interacción con los servicios de gRPC.
* [gRPCui](https://github.com/fullstorydev/grpcui) se basa en gRPCurl, y agrega una interfaz de usuario web interactiva para gRPC similar a herramientas como Postman y la interfaz de usuario de Swagger.

En este artículo se describe cómo:

* Descargar e instalar gRPCurl y gRPCui.
* Configurar la reflexión gRPC con una aplicación gRPC de ASP.NET Core.
* Detectar y probar servicios gRPC con `grpcurl`.
* Interactuar con servicios gRPC a través de un explorador mediante `grpcui`.

## <a name="about-grpcurl"></a>Acerca de gRPCurl

gRPCurl es una herramienta de línea de comandos creada por la comunidad de gRPC. Sus características incluyen:

* Llamada a servicios gRPC, incluidos los servicios de streaming.
* Detección de servicios mediante la [reflexión gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Enumeración y descripción de servicios gRPC.
* Funciona con servidores seguros (TLS) e inseguros (texto sin formato).

Para más información sobre la descarga e instalación de `grpcurl`, consulte la [página principal de GitHub de gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

![Línea de comandos de gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Configuración de la reflexión gRPC

`grpcurl` debe conocer el contrato de servicios de Protobuf para poder llamarlos. Existen dos formas de hacerlo:

* Configurando una [reflexión gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) en el servidor. gRPCurl detectará automáticamente los contratos de servicio.
* Especificando archivos `.proto` en los argumentos de la línea de comandos en gRPCurl.

Es más fácil usar gRPCurl con una reflexión gRPC. La reflexión gRPC agrega un nuevo servicio gRPC a la aplicación al que los clientes pueden llamar para detectar servicios.

gRPC de ASP.NET Core tiene compatibilidad integrada con la reflexión gRPC con el paquete [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection). Para configurar la reflexión en una aplicación:

* Agregue una referencia de paquete `Grpc.AspNetCore.Server.Reflection`.
* Registre la reflexión en `Startup.cs`:
  * `AddGrpcReflection` para registrar los servicios que habilitan la reflexión.
  * `MapGrpcReflectionService` para agregar un punto de conexión de servicio de reflexión.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Cuando la reflexión gRPC está configurada:

* Un servicio de reflexión gRPC se agrega a la aplicación de servidor.
* Las aplicaciones cliente que admiten la reflexión gRPC pueden llamar al servicio de reflexión para detectar los servicios hospedados por el servidor.
* Los servicios gRPC se siguen llamando desde el cliente. La reflexión solo habilita la detección de servicios, y no omite la seguridad del servidor. Los puntos de conexión protegidos por la [autenticación y autorización](xref:grpc/authn-and-authz) requieren que el autor de la llamada pase unas credenciales determinadas para poder llamar al punto de conexión correctamente.

## <a name="use-grpcurl"></a>Use `grpcurl`

En el argumento `-help` se explican las opciones de la línea de comandos `grpcurl`:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Detección de servicios

Utilice el verbo `describe` para ver los servicios definidos por el servidor:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Ejemplo anterior:

* Ejecuta el verbo `describe` en el servidor `localhost:5001`.
* Imprime los servicios y métodos devueltos por la reflexión gRPC.
  * `Greeter` es un servicio implementado por la aplicación.
  * `ServerReflection` es el servicio agregado por el paquete `Grpc.AspNetCore.Server.Reflection`.

Combine `describe` con un nombre de servicio, método o mensaje para ver sus detalles:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Llamada a servicios gRPC

Llame a un servicio gRPC especificando un nombre de método y servicio, junto con un argumento JSON que representa el mensaje de solicitud. El formato JSON se convierte a Protobuf y se envía al servicio.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

En el ejemplo anterior:

* El argumento `-d` especifica un mensaje de solicitud con JSON. Este argumento debe ir delante de la dirección del servidor y el nombre del método.
* Llama al método `SayHello` en el servicio `greeter.Greeter`.
* Imprime el mensaje de respuesta como JSON.

## <a name="about-grpcui"></a>Acerca de gRPCui

gRPCui es una interfaz de usuario web interactiva para gRPC. Se basa en gRPCurl, y ofrece una interfaz gráfica de usuario para detectar y probar servicios gRPC, de forma similar a herramientas HTTP como Postman o la interfaz de usuario de Swagger.

Para más información sobre la descarga e instalación de `grpcui`, consulte la [página principal de GitHub de gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Usar `grpcui`

Ejecute `grpcui` con la dirección del servidor con el que se va a interactuar como argumento:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

La herramienta inicia una ventana del explorador con la interfaz de usuario web interactiva. Los servicios gRPC se detectan automáticamente mediante la reflexión de gRPC.

![Interfaz de usuario web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Recursos adicionales

* [Página principal de GitHub de gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Página principal de GitHub de gRPCui](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
