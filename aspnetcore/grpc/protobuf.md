---
title: Creación de mensajes de Protobuf para aplicaciones .NET
author: jamesnk
description: Obtenga información sobre cómo crear mensajes de Protobuf para aplicaciones .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: ea46e04bc4aa6269efbf8917d5f32194402a66ef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722701"
---
# <a name="create-protobuf-messages-for-net-apps"></a>Creación de mensajes de Protobuf para aplicaciones .NET

Por [James Newton-King](https://twitter.com/jamesnk) y [Mark Rendle](https://twitter.com/markrendle)

gRPC usa [Protobuf](https://developers.google.com/protocol-buffers) como lenguaje de definición de interfaz (IDL). El IDL de Protobuf es un formato neutro de lenguaje para especificar los mensajes que envían y reciben los servicios gRPC. Los mensajes de Protobuf se definen en archivos `.proto`. En este documento se explica cómo se relacionan los conceptos de Protobuf con .NET.

## <a name="protobuf-messages"></a>Mensajes de Protobuf

Los mensajes son el objeto de transferencia de datos principal en Protobuf. Son conceptualmente similares a las clases .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

La definición de mensaje anterior especifica tres campos como pares de nombre-valor. Al igual que las propiedades de los tipos .NET, cada campo tiene un nombre y un tipo. El tipo del campo puede ser un [tipo de valor escalar de Protobuf](#scalar-value-types), por ejemplo, `int32` u otro mensaje.

Además de un nombre, cada campo de la definición del mensaje tiene un número único. Los números del campo se utilizan para identificar los campos cuando el mensaje se serializa en Protobuf. Serializar un número pequeño es más rápido que serializar todo el nombre del campo. Dado que los números del campo identifican un campo, es importante tener cuidado al cambiarlos. Para obtener más información sobre cómo cambiar los mensajes de Protobuf, vea <xref:grpc/versioning>.

Cuando se compila una aplicación, las herramientas de Protobuf generan tipos .NET a partir de archivos `.proto`. El mensaje `Person` genera una clase .NET:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Para obtener más información sobre los mensajes de Protobuf, vea la [Guía del lenguaje de Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Tipos de valor escalares

Protobuf admite una gama de tipos de valores escalares nativos. En la tabla siguiente se enumeran todos con su tipo C# equivalente:

| Tipo de Protobuf | Tipo de C#      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

Los valores escalares siempre tienen un valor predeterminado y no se pueden establecer en `null`. Esta restricción incluye `string` y `ByteString`, que son clases de C#. `string` se establece de forma predeterminada en un valor de cadena vacía y `ByteString`, en un valor de bytes vacío. Al intentar establecerlos en `null`, se produce un error.

Se pueden usar [tipos de contenedores que admiten valores NULL](#nullable-types) para admitir dichos valores.

### <a name="dates-and-times"></a>Fechas y horas

Los tipos escalares nativos no proporcionan valores de fecha y hora, equivalentes a los valores <xref:System.DateTimeOffset>, <xref:System.DateTime> y <xref:System.TimeSpan> de .NET. Estos tipos se pueden especificar mediante el uso de algunas de las extensiones de *Tipos conocidos* de Protobuf. Estas extensiones proporcionan compatibilidad con el entorno de ejecución y la generación de código para los tipos de campo complejos en las plataformas admitidas.

En la tabla siguiente se muestran los tipos de fecha y hora:

| Tipo de .NET        | Tipo conocido de Protobuf    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

Las propiedades generadas en la clase C# no son los tipos de fecha y hora de .NET. Las propiedades usan las clases `Timestamp` y `Duration` en el espacio de nombres `Google.Protobuf.WellKnownTypes`. Estas clases proporcionan métodos para realizar conversiones a `DateTimeOffset`, `DateTime` y `TimeSpan`, y desde estas.

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> El tipo `Timestamp` funciona con horas UTC. Los valores `DateTimeOffset` siempre tienen un desplazamiento de cero, y la propiedad `DateTime.Kind` siempre es `DateTimeKind.Utc`.

### <a name="nullable-types"></a>Tipos que aceptan valores NULL

La generación de código de Protobuf para C# usa los tipos nativos, como `int` para `int32`. Por lo tanto, los valores siempre se incluyen y no pueden ser `null`.

En el caso de los valores que requieren elementos `null` explícitos, como el uso de `int?` en el código C#, los Tipos conocidos de Protobuf incluyen contenedores que se compilan en tipos de C# que admiten un valor NULL. Para usarlos, importe `wrappers.proto` en el archivo `.proto`, como el código siguiente:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Los tipos `wrappers.proto` no se exponen en las propiedades generadas. Protobuf los asigna automáticamente a los tipos adecuados que aceptan valores NULL de .NET en los mensajes de C#. Por ejemplo, un campo `google.protobuf.Int32Value` genera una propiedad `int?`. Las propiedades de tipo de referencia como `string` y `ByteString` no se alteran, excepto que se les puede asignar `null` sin errores.

En la tabla siguiente se muestra la lista completa de tipos de contenedor con su tipo C# equivalente:

| Tipo de C#      | Contenedor de Tipo conocido       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a>Bytes

Las cargas binarias se admiten en Protobuf con el tipo de valor escalar `bytes`. Una propiedad generada en C# usa `ByteString` como tipo de propiedad.

Use `ByteString.CopyFrom(byte[] data)` para crear una instancia a partir de una matriz de bytes:

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

El acceso a los datos de `ByteString` se realiza directamente por medio de `ByteString.Span` o `ByteString.Memory`. También se puede llamar a `ByteString.ToByteArray()` para volver a convertir una instancia en una matriz de bytes:

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Decimals

Protobuf no admite de forma nativa el tipo `decimal` de .NET, solo `double` y `float`. Existe un debate en curso en el proyecto Protobuf sobre la posibilidad de agregar un tipo decimal estándar a los Tipos conocidos, con compatibilidad de plataforma para los lenguajes y marcos que lo admitan. Todavía no se ha implementado nada.

Es posible crear una definición de mensaje para representar el tipo `decimal` que funciona para la serialización segura entre clientes y servidores .NET. Sin embargo, los desarrolladores de otras plataformas tendrían que conocer el formato que se usa e implementar su propio control.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Creación de un tipo decimal personalizado para Protobuf

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

El campo `nanos` representa los valores de `0.999_999_999` a `-0.999_999_999`. Por ejemplo, el valor `decimal` `1.5m` se representaría como `{ units = 1, nanos = 500_000_000 }`. Este es el motivo por el que el campo `nanos` de este ejemplo usa el tipo `sfixed32`, que codifica de forma más eficaz que `int32` para los valores más grandes. Si el campo `units` es negativo, el campo `nanos` también debe serlo.

> [!NOTE]
> Existen varios algoritmos para codificar los valores `decimal` como cadenas de bytes, pero este mensaje es más fácil de entender que cualquiera de ellos. Los formatos big-endian o little-endian en plataformas distintas no afectan a los valores.

La conversión entre este tipo y el tipo `decimal` de BCL podría implementarse en C# de la siguiente manera:

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a>Colecciones

### <a name="lists"></a>Listas

Las listas de Protobuf se especifican mediante el uso de la palabra clave de prefijo `repeated` en un campo. En el ejemplo siguiente se muestra cómo crear una lista:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

En el código generado, los campos `repeated` se representan mediante el tipo genérico `Google.Protobuf.Collections.RepeatedField<T>`.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>. Por lo tanto, puede usar consultas LINQ o convertirlas en una matriz o lista. Las propiedades `RepeatedField<T>` no tienen un establecedor público. Los elementos deben agregarse a la colección existente.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Diccionarios

El tipo <xref:System.Collections.Generic.IDictionary%602> de .NET se representa en Protobuf mediante `map<key_type, value_type>`.

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

En el código de .NET generado, los campos `map` se representan mediante el tipo genérico `Google.Protobuf.Collections.MapField<TKey, TValue>`. `MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>. Al igual que las propiedades `repeated`, las propiedades `map` no tienen un establecedor público. Los elementos deben agregarse a la colección existente.

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a>Mensajes no estructurados y condicionales

Protobuf es un formato de mensajería de contrato primero. Los mensajes de una aplicación, incluidos los campos y los tipos, deben especificarse en archivos `.proto` cuando se compila la aplicación. El diseño de contrato primero de Protobuf es ideal para aplicar el contenido del mensaje, pero puede resultar limitado en los casos en los que no se requiere un contrato estricto:

* Mensajes con cargas desconocidas. Por ejemplo, un mensaje con un campo que podría contener cualquier mensaje.
* Mensajes condicionales. Por ejemplo, un mensaje devuelto desde un servicio gRPC podría ser un resultado correcto o un error.
* Valores dinámicos. Por ejemplo, un mensaje con un campo que contiene una colección no estructurada de valores, similar a JSON.

Protobuf ofrece tipos y características de lenguaje para admitir estos escenarios.

### <a name="any"></a>Any

El tipo `Any` permite usar mensajes como tipos insertados sin tener su definición de `.proto`. Para usar el tipo `Any`, importe `any.proto`.

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a>Oneof

Los campos `oneof` son una característica de idioma. El compilador controla la palabra clave `oneof` cuando genera la clase del mensaje. El uso de `oneof` para especificar un mensaje de respuesta que pueda devolver un elemento `Person` o `Error` podría tener un aspecto similar al siguiente:

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

Los campos del conjunto de `oneof` deben tener números de campo únicos en la declaración de mensaje global.

Cuando se usa `oneof`, el código C# generado incluye una enumeración que especifica cuál de los campos se ha establecido. Puede probar la enumeración para buscar el campo que se establece. Los campos que no se establecen devuelven `null` o el valor predeterminado, en lugar de generar una excepción.

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a>Value

El tipo `Value` representa un valor de tipo dinámico. Puede ser `null`, un número, una cadena, un valor booleano, un diccionario de valores (`Struct`) o una lista de valores (`ValueList`). `Value` es un Tipo conocido de Protobuf que usa la característica de `oneof` descrita anteriormente. Para usar el tipo `Value`, importe `struct.proto`.

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

El uso de `Value` de forma directa puede resultar detallado. Una manera alternativa de usar `Value` es con la compatibilidad integrada de Protobuf para asignar mensajes a JSON. Los tipos `JsonFormatter` y `JsonWriter` de Protobuf se pueden usar con cualquier mensaje de Protobuf. `Value` es especialmente adecuado para convertirse a JSON y desde este.

Este es el equivalente JSON del código anterior:

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Recursos adicionales

* [Guía del lenguaje de Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
