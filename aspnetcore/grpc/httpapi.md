---
title: Creación de API web JSON desde gRPC
author: jamesnk
description: Obtenga información sobre cómo crear API HTTP JSON para servicios gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: fa4e7489920338344b78874690e64d4080b5a719
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805588"
---
# <a name="create-json-web-apis-from-grpc"></a>Creación de API web JSON desde gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> La API HTTP de gRPC es un proyecto experimental, no un producto confirmado. Queremos:
>
> * Comprobar que nuestro enfoque para crear API web JSON para servicios gRPC funciona.
> * Recibir comentarios sobre si este enfoque es útil para los desarrolladores de .NET.
>
> [Deje sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167) para asegurarnos de que creamos algo que gusta a los desarrolladores y los hace productivos.

gRPC es una forma moderna de comunicarse entre aplicaciones. gRPC usa HTTP/2, streaming, Protobuf y contratos de mensaje para crear servicios en tiempo real de alto rendimiento.

Una limitación de gRPC es que no se puede usar en todas las plataformas. Los exploradores no son totalmente compatibles con HTTP/2, por lo que REST y JSON son la manera principal de obtener datos en aplicaciones de explorador. Incluso con las ventajas que aporta gRPC, REST y JSON ocupan un lugar importante en las aplicaciones modernas. La compilación de API web JSON ***y*** gRPC agrega una sobrecarga no deseada al desarrollo de aplicaciones.

En este documento se explica cómo crear API web JSON con servicios gRPC.

## <a name="grpc-http-api"></a>API HTTP de gRPC

La API HTTP de gRPC es una extensión experimental para ASP.NET Core que crea API JSON de RESTful para servicios gRPC. Una vez configurada, la API HTTP de gRPC permite que las aplicaciones llamen a servicios gRPC con conceptos conocidos de HTTP:

* Verbos HTTP
* Enlace de parámetros de dirección URL
* Solicitudes y respuestas JSON

gRPC también se puede usar para llamar a servicios.

### <a name="usage"></a>Uso

1. Agregue una referencia de paquete a [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Registre los servicios en *Startup.cs* con `AddGrpcHttpApi`.
1. Agregue los archivos [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) y [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) al proyecto.
1. Anote los métodos gRPC en los archivos *.proto* con enlaces y rutas HTTP:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

El método gRPC `SayHello` ya se puede invocar como gRPC+protobuf y como una API HTTP:

* Solicitud: `HTTP/1.1 GET /v1/greeter/world`
* Respuesta: `{ "message": "Hello world" }`

Los registros de servidor muestran que un servicio gRPC ejecuta la llamada HTTP. La API HTTP de gRPC asigna la solicitud HTTP entrante a un mensaje de gRPC y, luego, convierte el mensaje de respuesta en JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Este es un ejemplo básico. Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para conocer más opciones de personalización.

### <a name="grpc-http-api-vs-grpc-web"></a>API HTTP de gRPC frente a gRPC-Web

Tanto la API HTTP de gRPC como gRPC-Web permiten llamar a servicios gRPC desde un explorador, aunque de manera diferente:

* gRPC-Web permite a las aplicaciones de explorador llamar a servicios gRPC desde el explorador con el cliente gRPC-Web y Protobuf. gRPC-Web requiere que la aplicación de explorador genere un cliente gRPC y tiene la ventaja de que envía mensajes de Protobuf pequeños y rápidos.
* La API HTTP de gRPC permite a las aplicaciones de explorador llamar a servicios gRPC como si fueran API de RESTful con JSON. No es necesario que la aplicación de explorador genere un cliente gRPC o que disponga de información sobre gRPC.

No se crea ningún cliente generado para la API HTTP de gRPC. Se puede llamar al servicio `Greeter` anterior mediante las API de JavaScript del explorador:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Estado experimental

La API HTTP de gRPC es un experimento. Todavía no está completa y no se admite. Nos interesa esta tecnología y las capacidades que ofrece a los desarrolladores de aplicaciones para crear rápidamente servicios gRPC y JSON al mismo tiempo. No hay ningún compromiso para completar la API HTTP de gRPC.

Queremos valorar el interés que tienen los desarrolladores por la API HTTP de gRPC. Si le atrae la API HTTP de gRPC, [envíenos sus comentarios](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) es otra tecnología para crear API JSON de RESTful desde servicios gRPC. Usa las mismas anotaciones *.proto* para asignar conceptos HTTP a servicios gRPC.

La principal diferencia entre grpc-gateway y la API HTTP de gRPC es que grpc-gateway usa la generación de código para crear un servidor proxy inverso. El proxy inverso traslada las llamadas de RESTful a gRPC y, luego, las envía al servicio gRPC.

Para obtener información sobre la instalación y el uso de grpc-gateway, consulte la [documentación de grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de google.api.HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
