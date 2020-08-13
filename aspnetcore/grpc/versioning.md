---
title: Control de versiones de servicios gRPC
author: jamesnk
description: Obtenga información sobre cómo controlar las versiones de servicios gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 087ce449b0d8ab1eaf3434ad64feeff78c1fee22
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015964"
---
# <a name="versioning-grpc-services"></a>Control de versiones de servicios gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

Las nuevas características agregadas a una aplicación pueden requerir que los servicios gRPC que se proporcionan a los clientes cambien y, en ocasiones, que lo hagan de forma inesperada y repentina. Si cambian los servicios gRPC:

* Se debe tener en cuenta cómo afectan los cambios a los clientes.
* Se debe implementar una estrategia de control de versiones para admitir los cambios.

## <a name="backwards-compatibility"></a>Compatibilidad con versiones anteriores

El protocolo gRPC está diseñado para admitir servicios que cambian con el tiempo. Por lo general, las adiciones a los servicios y métodos gRPC suponen cambios secundarios. Los cambios secundarios permiten a los clientes existentes seguir trabajando como de costumbre. La modificación o la eliminación de los servicios gRPC producen cambios importantes. Si un servicio gRPC sufre cambios importantes, los clientes que lo usen deberán actualizarse y volver a implementarse.

La realización de cambios secundarios en un servicio tiene una serie de ventajas:

* Los clientes existentes continúan ejecutándose.
* Evita tener que notificar a los clientes los cambios importantes y tener que actualizarlos.
* Solo es necesario documentar y mantener una versión del servicio.

### <a name="non-breaking-changes"></a>Cambios secundarios

Los siguientes cambios son secundarios a nivel del protocolo gRPC y a nivel binario de .NET.

* **Adición de un nuevo servicio**
* **Adición de un nuevo método en un servicio**
* **Adición de un campo en un mensaje de solicitud**: los campos agregados en un mensaje de solicitud se deserializan con el [valor predeterminado](https://developers.google.com/protocol-buffers/docs/proto3#default) en el servidor cuando no se fija un valor. Para ser un cambio secundario, el servicio debe funcionar correctamente en los casos en los que los clientes más antiguos no establezcan el nuevo campo.
* **Adición de un campo en un mensaje de respuesta**: los campos agregados en un mensaje de respuesta se deserializan en la colección [campos desconocidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) del mensaje del cliente.
* **Adición de un valor en una enumeración**: las enumeraciones se serializan como un valor numérico. Los nuevos valores de enumeración se deserializan en el cliente en el valor de enumeración sin un nombre de enumeración. Para que sea un cambio secundario, los clientes más antiguos deben ejecutarse correctamente al recibir el nuevo valor de enumeración.

### <a name="binary-breaking-changes"></a>Cambios binarios importantes

Los siguientes cambios son secundarios en el nivel del protocolo gRPC, pero es necesario actualizar el cliente si pasa a usar el contrato *.proto* o el ensamblado .NET de cliente más reciente. La compatibilidad binaria es importante si tiene previsto publicar una biblioteca de gRPC en NuGet.

* **Eliminación de un campo**: los valores de un campo eliminado se deserializan en [campos desconocidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) de un mensaje. No se trata de un cambio importante del protocolo gRPC, pero el cliente debe actualizarse si pasa a usar el contrato más reciente. Es importante que un número de campo eliminado no se vuelva a usar accidentalmente en el futuro. Para asegurarse de que esto no suceda, especifique en el mensaje los nombres y números de los campos eliminados con la palabra clave [reservada](https://developers.google.com/protocol-buffers/docs/proto3#reserved) de Protobuf.
* **Cambio de nombre de un mensaje**: los nombres de los mensajes no se envían normalmente en la red, por lo que no se trata de un cambio importante del protocolo gRPC. El cliente deberá actualizarse si pasa a usar el contrato más reciente. Una situación en la que los nombres de mensaje **sí** se envían en la red es en el caso de los campos [Any](https://developers.google.com/protocol-buffers/docs/proto3#any), cuando el nombre del mensaje se usa para identificar el tipo de mensaje.
* **Cambio de csharp_namespace**: al modificar `csharp_namespace`, cambiará el espacio de nombres de los tipos .NET generados. No se trata de un cambio importante del protocolo gRPC, pero el cliente debe actualizarse si pasa a usar el contrato más reciente.

### <a name="protocol-breaking-changes"></a>Cambios importantes de protocolo

Los elementos siguientes son cambios binarios y de protocolo importantes:

* **Cambio de nombre de un campo**: en el contenido de Protobuf, los nombres de campos solo se usan en el código generado. El número de campo se usa para identificar los campos de la red. La modificación del nombre de un campo no es un cambio importante en el protocolo para Protobuf. Sin embargo, si un servidor usa contenido JSON, modificar el nombre de un campo sí se considera un cambio importante.
* **Cambio de un tipo de datos de campo**: si cambia el tipo de datos de un campo por un [tipo incompatible](https://developers.google.com/protocol-buffers/docs/proto3#updating), se producirán errores al deserializar el mensaje. Aunque el nuevo tipo de datos sea compatible, es probable que el cliente deba actualizarse para admitir el nuevo tipo si pasa a usar el contrato más reciente.
* **Cambio de un número de campo**: con las cargas de Protobuf, el número de campo se usa para identificar los campos de la red.
* **Cambio de nombre de un paquete, un servicio o un método**: gRPC se usa el nombre del paquete, del servicio y del método para generar la dirección URL. El cliente recibe el estado *SIN IMPLEMENTAR* del servidor.
* **Eliminación de un servicio o método**: el cliente recibe el estado *SIN IMPLEMENTAR* del servidor al llamar al método eliminado.

### <a name="behavior-breaking-changes"></a>Cambios importantes de comportamiento

Al realizar cambios secundarios, también debe tener en cuenta si los clientes más antiguos pueden seguir trabajando con el nuevo comportamiento del servicio. Por ejemplo, agregar un nuevo campo a un mensaje de solicitud:

* No es un cambio importante de protocolo.
* Devuelve un estado de error en el servidor si el nuevo campo no está establecido y se convierte en un cambio importante para los clientes antiguos.

La compatibilidad con el comportamiento la determina el código específico de su aplicación.

## <a name="version-number-services"></a>Servicios de número de versión

Es conveniente procurar que los servicios sigan siendo compatibles con versiones anteriores de los clientes antiguos. Es posible que los cambios realizados en la aplicación requieran cambios importantes. Introducir cambios en los clientes antiguos y exigir su actualización con un servicio nuevo no es una buena experiencia de usuario. Una manera de mantener la compatibilidad con versiones anteriores al realizar cambios importantes es publicar varias versiones de un servicio.

gRPC admite un especificador de [paquete](https://developers.google.com/protocol-buffers/docs/proto3#packages) opcional, que funciona de manera muy similar a un espacio de nombres de .NET. De hecho, `package` se usará como el espacio de nombres de .NET para los tipos .NET generados si `option csharp_namespace` no se establece en el archivo *.proto*. El paquete se puede usar para especificar un número de versión para el servicio y sus mensajes:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

El nombre del paquete se combina con el nombre del servicio para identificar una dirección de servicio. Una dirección de servicio permite hospedar varias versiones de un servicio en paralelo:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Las implementaciones del servicio con versiones se registran en *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Incluir un número de versión en el nombre del paquete le permite publicar una versión *v2* de su servicio con cambios importantes, a la vez que continúa admitiendo clientes más antiguos que llaman a la versión *v1*. Una vez que los clientes se hayan actualizado para usar el servicio *v2*, podrá eliminar la versión anterior. A la hora de planificar la publicación de varias versiones de un servicio:

* Evite introducir cambios importantes, si es posible.
* No actualice el número de versión a menos que realice cambios importantes.
* Actualice el número de versión cuando realice cambios importantes.

Al publicar varias versiones de un servicio, este se duplica. Para reducir la duplicación, le recomendamos mover la lógica de negocios de las implementaciones de servicio a una ubicación centralizada que se pueda reutilizar en las implementaciones antiguas y nuevas:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Los servicios y mensajes generados con distintos nombres de paquetes son **diferentes tipos de .NET**. Mover la lógica de negocios a una ubicación centralizada requiere la asignación de mensajes a tipos comunes.
