---
title: Creación de mensajes de Protobuf para aplicaciones .NET
author: jamesnk
description: Obtenga información sobre cómo crear mensajes de Protobuf para aplicaciones .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/protobuf
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058901"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="e0bd2-103">Creación de mensajes de Protobuf para aplicaciones .NET</span><span class="sxs-lookup"><span data-stu-id="e0bd2-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="e0bd2-104">Por [James Newton-King](https://twitter.com/jamesnk) y [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="e0bd2-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="e0bd2-105">gRPC usa [Protobuf](https://developers.google.com/protocol-buffers) como lenguaje de definición de interfaz (IDL).</span><span class="sxs-lookup"><span data-stu-id="e0bd2-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="e0bd2-106">El IDL de Protobuf es un formato neutro de lenguaje para especificar los mensajes que envían y reciben los servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="e0bd2-107">Los mensajes de Protobuf se definen en archivos `.proto`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="e0bd2-108">En este documento se explica cómo se relacionan los conceptos de Protobuf con .NET.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="e0bd2-109">Mensajes de Protobuf</span><span class="sxs-lookup"><span data-stu-id="e0bd2-109">Protobuf messages</span></span>

<span data-ttu-id="e0bd2-110">Los mensajes son el objeto de transferencia de datos principal en Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="e0bd2-111">Son conceptualmente similares a las clases .NET.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="e0bd2-112">La definición de mensaje anterior especifica tres campos como pares de nombre-valor.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="e0bd2-113">Al igual que las propiedades de los tipos .NET, cada campo tiene un nombre y un tipo.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="e0bd2-114">El tipo del campo puede ser un [tipo de valor escalar de Protobuf](#scalar-value-types), por ejemplo, `int32` u otro mensaje.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="e0bd2-115">Además de un nombre, cada campo de la definición del mensaje tiene un número único.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="e0bd2-116">Los números del campo se utilizan para identificar los campos cuando el mensaje se serializa en Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="e0bd2-117">Serializar un número pequeño es más rápido que serializar todo el nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="e0bd2-118">Dado que los números del campo identifican un campo, es importante tener cuidado al cambiarlos.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="e0bd2-119">Para obtener más información sobre cómo cambiar los mensajes de Protobuf, vea <xref:grpc/versioning>.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="e0bd2-120">Cuando se compila una aplicación, las herramientas de Protobuf generan tipos .NET a partir de archivos `.proto`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="e0bd2-121">El mensaje `Person` genera una clase .NET:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="e0bd2-122">Para obtener más información sobre los mensajes de Protobuf, vea la [Guía del lenguaje de Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="e0bd2-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="e0bd2-123">Tipos de valor escalares</span><span class="sxs-lookup"><span data-stu-id="e0bd2-123">Scalar Value Types</span></span>

<span data-ttu-id="e0bd2-124">Protobuf admite una gama de tipos de valores escalares nativos.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="e0bd2-125">En la tabla siguiente se enumeran todos con su tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="e0bd2-126">Tipo de Protobuf</span><span class="sxs-lookup"><span data-stu-id="e0bd2-126">Protobuf type</span></span> | <span data-ttu-id="e0bd2-127">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="e0bd2-127">C# type</span></span>      |
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

<span data-ttu-id="e0bd2-128">Los valores escalares siempre tienen un valor predeterminado y no se pueden establecer en `null`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="e0bd2-129">Esta restricción incluye `string` y `ByteString`, que son clases de C#.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="e0bd2-130">`string` se establece de forma predeterminada en un valor de cadena vacía y `ByteString`, en un valor de bytes vacío.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="e0bd2-131">Al intentar establecerlos en `null`, se produce un error.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="e0bd2-132">Se pueden usar [tipos de contenedores que admiten valores NULL](#nullable-types) para admitir dichos valores.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="e0bd2-133">Fechas y horas</span><span class="sxs-lookup"><span data-stu-id="e0bd2-133">Dates and times</span></span>

<span data-ttu-id="e0bd2-134">Los tipos escalares nativos no proporcionan valores de fecha y hora, equivalentes a los valores <xref:System.DateTimeOffset>, <xref:System.DateTime> y <xref:System.TimeSpan> de .NET.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="e0bd2-135">Estos tipos se pueden especificar mediante el uso de algunas de las extensiones de *Tipos conocidos* de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="e0bd2-136">Estas extensiones proporcionan compatibilidad con el entorno de ejecución y la generación de código para los tipos de campo complejos en las plataformas admitidas.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="e0bd2-137">En la tabla siguiente se muestran los tipos de fecha y hora:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="e0bd2-138">Tipo de .NET</span><span class="sxs-lookup"><span data-stu-id="e0bd2-138">.NET type</span></span>        | <span data-ttu-id="e0bd2-139">Tipo conocido de Protobuf</span><span class="sxs-lookup"><span data-stu-id="e0bd2-139">Protobuf Well-Known Type</span></span>    |
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

<span data-ttu-id="e0bd2-140">Las propiedades generadas en la clase C# no son los tipos de fecha y hora de .NET.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="e0bd2-141">Las propiedades usan las clases `Timestamp` y `Duration` en el espacio de nombres `Google.Protobuf.WellKnownTypes`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="e0bd2-142">Estas clases proporcionan métodos para realizar conversiones a `DateTimeOffset`, `DateTime` y `TimeSpan`, y desde estas.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="e0bd2-143">El tipo `Timestamp` funciona con horas UTC.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="e0bd2-144">Los valores `DateTimeOffset` siempre tienen un desplazamiento de cero, y la propiedad `DateTime.Kind` siempre es `DateTimeKind.Utc`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="e0bd2-145">Tipos que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="e0bd2-145">Nullable types</span></span>

<span data-ttu-id="e0bd2-146">La generación de código de Protobuf para C# usa los tipos nativos, como `int` para `int32`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="e0bd2-147">Por lo tanto, los valores siempre se incluyen y no pueden ser `null`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="e0bd2-148">En el caso de los valores que requieren elementos `null` explícitos, como el uso de `int?` en el código C#, los Tipos conocidos de Protobuf incluyen contenedores que se compilan en tipos de C# que admiten un valor NULL.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="e0bd2-149">Para usarlos, importe `wrappers.proto` en el archivo `.proto`, como el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="e0bd2-150">Los tipos `wrappers.proto` no se exponen en las propiedades generadas.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="e0bd2-151">Protobuf los asigna automáticamente a los tipos adecuados que aceptan valores NULL de .NET en los mensajes de C#.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="e0bd2-152">Por ejemplo, un campo `google.protobuf.Int32Value` genera una propiedad `int?`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="e0bd2-153">Las propiedades de tipo de referencia como `string` y `ByteString` no se alteran, excepto que se les puede asignar `null` sin errores.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="e0bd2-154">En la tabla siguiente se muestra la lista completa de tipos de contenedor con su tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="e0bd2-155">Tipo de C#</span><span class="sxs-lookup"><span data-stu-id="e0bd2-155">C# type</span></span>      | <span data-ttu-id="e0bd2-156">Contenedor de Tipo conocido</span><span class="sxs-lookup"><span data-stu-id="e0bd2-156">Well-Known Type wrapper</span></span>       |
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

### <a name="bytes"></a><span data-ttu-id="e0bd2-157">Bytes</span><span class="sxs-lookup"><span data-stu-id="e0bd2-157">Bytes</span></span>

<span data-ttu-id="e0bd2-158">Las cargas binarias se admiten en Protobuf con el tipo de valor escalar `bytes`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="e0bd2-159">Una propiedad generada en C# usa `ByteString` como tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="e0bd2-160">Use `ByteString.CopyFrom(byte[] data)` para crear una instancia a partir de una matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="e0bd2-161">El acceso a los datos de `ByteString` se realiza directamente por medio de `ByteString.Span` o `ByteString.Memory`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="e0bd2-162">También se puede llamar a `ByteString.ToByteArray()` para volver a convertir una instancia en una matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="e0bd2-163">Decimals</span><span class="sxs-lookup"><span data-stu-id="e0bd2-163">Decimals</span></span>

<span data-ttu-id="e0bd2-164">Protobuf no admite de forma nativa el tipo `decimal` de .NET, solo `double` y `float`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="e0bd2-165">Existe un debate en curso en el proyecto Protobuf sobre la posibilidad de agregar un tipo decimal estándar a los Tipos conocidos, con compatibilidad de plataforma para los lenguajes y marcos que lo admitan.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="e0bd2-166">Todavía no se ha implementado nada.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="e0bd2-167">Es posible crear una definición de mensaje para representar el tipo `decimal` que funciona para la serialización segura entre clientes y servidores .NET.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="e0bd2-168">Sin embargo, los desarrolladores de otras plataformas tendrían que conocer el formato que se usa e implementar su propio control.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="e0bd2-169">Creación de un tipo decimal personalizado para Protobuf</span><span class="sxs-lookup"><span data-stu-id="e0bd2-169">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="e0bd2-170">El campo `nanos` representa los valores de `0.999_999_999` a `-0.999_999_999`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="e0bd2-171">Por ejemplo, el valor `decimal` `1.5m` se representaría como `{ units = 1, nanos = 500_000_000 }`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="e0bd2-172">Este es el motivo por el que el campo `nanos` de este ejemplo usa el tipo `sfixed32`, que codifica de forma más eficaz que `int32` para los valores más grandes.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="e0bd2-173">Si el campo `units` es negativo, el campo `nanos` también debe serlo.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bd2-174">Existen varios algoritmos para codificar los valores `decimal` como cadenas de bytes, pero este mensaje es más fácil de entender que cualquiera de ellos.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="e0bd2-175">Los formatos big-endian o little-endian en plataformas distintas no afectan a los valores.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="e0bd2-176">La conversión entre este tipo y el tipo `decimal` de BCL podría implementarse en C# de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="e0bd2-177">Colecciones</span><span class="sxs-lookup"><span data-stu-id="e0bd2-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="e0bd2-178">Listas</span><span class="sxs-lookup"><span data-stu-id="e0bd2-178">Lists</span></span>

<span data-ttu-id="e0bd2-179">Las listas de Protobuf se especifican mediante el uso de la palabra clave de prefijo `repeated` en un campo.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="e0bd2-180">En el ejemplo siguiente se muestra cómo crear una lista:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="e0bd2-181">En el código generado, los campos `repeated` se representan mediante el tipo genérico `Google.Protobuf.Collections.RepeatedField<T>`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="e0bd2-182">`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="e0bd2-183">Por lo tanto, puede usar consultas LINQ o convertirlas en una matriz o lista.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="e0bd2-184">Las propiedades `RepeatedField<T>` no tienen un establecedor público.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="e0bd2-185">Los elementos deben agregarse a la colección existente.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="e0bd2-186">Diccionarios</span><span class="sxs-lookup"><span data-stu-id="e0bd2-186">Dictionaries</span></span>

<span data-ttu-id="e0bd2-187">El tipo <xref:System.Collections.Generic.IDictionary%602> de .NET se representa en Protobuf mediante `map<key_type, value_type>`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="e0bd2-188">En el código de .NET generado, los campos `map` se representan mediante el tipo genérico `Google.Protobuf.Collections.MapField<TKey, TValue>`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="e0bd2-189">`MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="e0bd2-190">Al igual que las propiedades `repeated`, las propiedades `map` no tienen un establecedor público.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="e0bd2-191">Los elementos deben agregarse a la colección existente.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-191">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="e0bd2-192">Mensajes no estructurados y condicionales</span><span class="sxs-lookup"><span data-stu-id="e0bd2-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="e0bd2-193">Protobuf es un formato de mensajería de contrato primero.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="e0bd2-194">Los mensajes de una aplicación, incluidos los campos y los tipos, deben especificarse en archivos `.proto` cuando se compila la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="e0bd2-195">El diseño de contrato primero de Protobuf es ideal para aplicar el contenido del mensaje, pero puede resultar limitado en los casos en los que no se requiere un contrato estricto:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="e0bd2-196">Mensajes con cargas desconocidas.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-196">Messages with unknown payloads.</span></span> <span data-ttu-id="e0bd2-197">Por ejemplo, un mensaje con un campo que podría contener cualquier mensaje.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="e0bd2-198">Mensajes condicionales.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-198">Conditional messages.</span></span> <span data-ttu-id="e0bd2-199">Por ejemplo, un mensaje devuelto desde un servicio gRPC podría ser un resultado correcto o un error.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="e0bd2-200">Valores dinámicos.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-200">Dynamic values.</span></span> <span data-ttu-id="e0bd2-201">Por ejemplo, un mensaje con un campo que contiene una colección no estructurada de valores, similar a JSON.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="e0bd2-202">Protobuf ofrece tipos y características de lenguaje para admitir estos escenarios.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="e0bd2-203">Any</span><span class="sxs-lookup"><span data-stu-id="e0bd2-203">Any</span></span>

<span data-ttu-id="e0bd2-204">El tipo `Any` permite usar mensajes como tipos insertados sin tener su definición de `.proto`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="e0bd2-205">Para usar el tipo `Any`, importe `any.proto`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-205">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="e0bd2-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="e0bd2-206">Oneof</span></span>

<span data-ttu-id="e0bd2-207">Los campos `oneof` son una característica de idioma.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="e0bd2-208">El compilador controla la palabra clave `oneof` cuando genera la clase del mensaje.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="e0bd2-209">El uso de `oneof` para especificar un mensaje de respuesta que pueda devolver un elemento `Person` o `Error` podría tener un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="e0bd2-210">Los campos del conjunto de `oneof` deben tener números de campo únicos en la declaración de mensaje global.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="e0bd2-211">Cuando se usa `oneof`, el código C# generado incluye una enumeración que especifica cuál de los campos se ha establecido.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="e0bd2-212">Puede probar la enumeración para buscar el campo que se establece.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="e0bd2-213">Los campos que no se establecen devuelven `null` o el valor predeterminado, en lugar de generar una excepción.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="e0bd2-214">Value</span><span class="sxs-lookup"><span data-stu-id="e0bd2-214">Value</span></span>

<span data-ttu-id="e0bd2-215">El tipo `Value` representa un valor de tipo dinámico.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="e0bd2-216">Puede ser `null`, un número, una cadena, un valor booleano, un diccionario de valores (`Struct`) o una lista de valores (`ValueList`).</span><span class="sxs-lookup"><span data-stu-id="e0bd2-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="e0bd2-217">`Value` es un Tipo conocido de Protobuf que usa la característica de `oneof` descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="e0bd2-218">Para usar el tipo `Value`, importe `struct.proto`.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-218">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="e0bd2-219">El uso de `Value` de forma directa puede resultar detallado.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="e0bd2-220">Una manera alternativa de usar `Value` es con la compatibilidad integrada de Protobuf para asignar mensajes a JSON.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="e0bd2-221">Los tipos `JsonFormatter` y `JsonWriter` de Protobuf se pueden usar con cualquier mensaje de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="e0bd2-222">`Value` es especialmente adecuado para convertirse a JSON y desde este.</span><span class="sxs-lookup"><span data-stu-id="e0bd2-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="e0bd2-223">Este es el equivalente JSON del código anterior:</span><span class="sxs-lookup"><span data-stu-id="e0bd2-223">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e0bd2-224">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e0bd2-224">Additional resources</span></span>

* [<span data-ttu-id="e0bd2-225">Guía del lenguaje de Protobuf</span><span class="sxs-lookup"><span data-stu-id="e0bd2-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
