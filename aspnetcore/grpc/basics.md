---
title: Servicios gRPC con C#
author: juntaoluo
description: Conozca los conceptos básicos a la hora de escribir servicios gRPC con C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 61ac7b84ec6a5eeb344cbf6f9a6712366be7d3c9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627083"
---
# <a name="grpc-services-with-c"></a>Servicios gRPC con C\#

En este documento se describen los conceptos necesarios para escribir de aplicaciones [gRPC](https://grpc.io/docs/guides/) en C#. Los temas aquí tratados son válidos para aplicaciones de gRPC basadas tanto en ASP.NET Core como en [C-core](https://grpc.io/blog/grpc-stacks).

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>Archivo .proto

gRPC usa un enfoque de contrato primero para el desarrollo de API. Los búferes de protocolo ("protobuf") se usan de forma predeterminada como el lenguaje de diseño de interfaz (IDL). El archivo *\*.proto* contiene lo siguiente:

* La definición del servicio gRPC
* Los mensajes enviados entre clientes y servidores

Para más información sobre la sintaxis de los archivos de protobuf, vea la [documentación oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Vamos a considerar, por ejemplo, el archivo *greeter.proto* que se usa en [Introducción a un servicio gRPC](xref:tutorials/grpc/grpc-start):

* Define un servicio `Greeter`.
* El servicio `Greeter` define una llamada a `SayHello`.
* `SayHello` envía un mensaje `HelloRequest` y recibe un mensaje `HelloReply`:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Adición de un archivo .proto a una aplicación de C\#

Para incluir el archivo *\*.proto* en un proyecto, hay que agregarlo al grupo de elementos `<Protobuf>`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

De forma predeterminada, una referencia `<Protobuf>` genera un cliente concreto y una clase base de servicio. El atributo `GrpcServices` del elemento de referencia se puede usar para limitar la generación de recursos de C#. Las opciones válidas de `GrpcServices` son:

* `Both` (valor predeterminado si no se especifica)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>Compatibilidad de herramientas de C# con archivos .proto

El paquete de herramientas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) es necesario para generar los activos de C# a partir de los *archivos \*.proto*. Los activos (archivos) generados:

* Se generan según sea necesario cada vez que el proyecto se compila.
* No se agregan al proyecto ni se protegen en el control de código fuente.
* Son un artefacto de compilación contenido en el directorio *obj*.

Este paquete es necesario en los proyectos tanto de servidor como de cliente. El metapaquete `Grpc.AspNetCore` incluye una referencia a `Grpc.Tools`. Los proyectos de servidor pueden agregar `Grpc.AspNetCore` por medio del administrador de paquetes de Visual Studio, o bien agregando un elemento `<PackageReference>` al archivo de proyecto:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Los proyectos de cliente, por su parte, deben hacer referencia directamente a `Grpc.Tools` junto con los demás paquetes necesarios para usar el cliente de gRPC. El paquete de herramientas no es necesario en tiempo de ejecución, de modo que la dependencia se marca con `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Activos de C# generados

El paquete de herramientas genera los tipos de C# que representan los mensajes definidos en los archivos *\*.proto* incluidos.

En el caso de los activos del lado servidor, se genera un tipo base de servicio abstracto. El tipo base contiene las definiciones de todas las llamadas a gRPC contenidas en el archivo *.proto*. Cree una implementación de servicio concreta que se derive de este tipo base e implemente la lógica de las llamadas a gRPC. Respecto a `greet.proto`, el ejemplo descrito anteriormente, se genera un tipo de elemento `GreeterBase` abstracto que contiene un método `SayHello` virtual. Una elemento `GreeterService` de una implementación concreta invalida el método e implementa la lógica que controla la llamada de gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

En cuanto a los activos del lado cliente, se genera un tipo de cliente concreto. Las llamadas de gRPC en el archivo *.proto* se traducen en métodos en el tipo concreto a los que se puede llamar. Respecto a `greet.proto`, el ejemplo descrito anteriormente, se genera un tipo de elemento `GreeterClient` concreto. Llame a `GreeterClient.SayHelloAsync` para iniciar una llamada de gRPC al servidor.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Se generarán activos de servidor y de cliente de forma predeterminada por cada archivo *\*.proto* incluido en el grupo de elementos `<Protobuf>`. Para garantizar que solo se generan activos de servidor en un proyecto de servidor, el atributo `GrpcServices` se establece en `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Y lo mismo sucede en los proyectos de cliente, donde el atributo se establece en `Client`.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
