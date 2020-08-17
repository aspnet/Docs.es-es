---
title: Escenarios avanzados de ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre escenarios avanzados en Blazor, incluido cómo incorporar la lógica RenderTreeBuilder manual en una aplicación.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051789"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>Escenarios avanzados de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Controlador de circuito de Blazor Server

Blazor Server permite que el código defina un *controlador de circuito*, que permite ejecutar el código en los cambios realizados en el estado del circuito de un usuario. Un controlador de circuito se implementa derivando de `CircuitHandler` y registrando la clase en el contenedor de servicios de la aplicación. El ejemplo siguiente de un controlador de circuito realiza un seguimiento de las conexiones abiertas de SignalR:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Los controladores de circuito se registran mediante DI. Las instancias con ámbito se crean por instancia de un circuito. Mediante el uso de `TrackingCircuitHandler` en el ejemplo anterior, se crea un servicio singleton porque se debe realizar un seguimiento del estado de todos los circuitos:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Si los métodos de un controlador de circuito personalizado producen una excepción no controlada, la excepción es grave para el circuito de Blazor Server. Para tolerar excepciones en el código de un controlador o en los métodos llamados, encapsule el código en una o varias instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.

Cuando un circuito finaliza porque un usuario se ha desconectado y el marco está limpiando el estado del circuito, el marco desecha el ámbito de DI del circuito. Al desechar el ámbito, se eliminan también todos los servicios de DI con ámbito del circuito que implementan <xref:System.IDisposable?displayProperty=fullName>. Si un servicio de DI produce una excepción no controlada durante la eliminación, el marco registra la excepción.

## <a name="manual-rendertreebuilder-logic"></a>Lógica Manual RenderTreeBuilder manual

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> proporciona métodos para manipular componentes y elementos, incluida la compilación manual de componentes en código de C#.

> [!NOTE]
> El uso de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> para crear componentes es un escenario avanzado. Un componente con formato incorrecto (por ejemplo, una etiqueta de marcado sin cerrar) puede dar como resultado un comportamiento indefinido.

Tenga en cuenta el componente `PetDetails` siguiente, que se puede integrar manualmente en otro componente:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

En el ejemplo siguiente, el bucle en el método `CreateComponent` genera tres componentes `PetDetails`. En métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> con un número de secuencia, los números de secuencia son números de línea de código fuente. El algoritmo de diferencia Blazor se basa en los números de secuencia correspondientes a líneas de código distintas, no a invocaciones de llamada distintas. Al crear un componente con métodos <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, codifique los argumentos para los números de secuencia. **El uso de un cálculo o un contador para generar el número de secuencia puede dar lugar a un rendimiento deficiente.** Para obtener más información, vea la sección [Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

Componente `BuiltContent`:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Los tipos en <xref:Microsoft.AspNetCore.Components.RenderTree> permiten el procesamiento de los *resultados* de las operaciones de representación. Estos son los detalles internos de la implementación del marco Blazor. Estos tipos se deben considerar *inestables* y deben estar sujetos a cambios en versiones futuras.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Los números de secuencia se relacionan con los números de línea de código y no con el orden de ejecución

Los archivos de componentes Razor (`.razor`) siempre se compilan. La compilación es una ventaja potencial sobre la interpretación de código porque el paso de compilación se puede usar para insertar información que mejore el rendimiento de las aplicaciones en tiempo de ejecución.

Un ejemplo clave de estas mejoras implica *números de secuencia*. Los números de secuencia indican al runtime qué resultados proceden a partir de qué líneas de código distintas y ordenadas. El runtime utiliza esta información con el fin de generar diferencias de árbol eficientes en tiempo lineal, que es mucho más rápido de lo que normalmente es posible para un algoritmo general de diferencia de árboles.

Considere el componente Razor siguiente (`.razor`):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

El código anterior se compila en algo similar a lo siguiente:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Cuando el código se ejecuta por primera vez, si `someFlag` es `true`, el generador recibe lo siguiente:

| Secuencia | Tipo      | Datos   |
| :------: | --------- | :----: |
| 0        | Nodo de texto | First  |
| 1        | Nodo de texto | Second |

Imagine que `someFlag` se convierte en `false` y que el marcado se representa de nuevo. Esta vez, el generador recibe lo siguiente:

| Secuencia | Tipo       | Datos   |
| :------: | ---------- | :----: |
| 1        | Nodo de texto  | Second |

Cuando el runtime realiza una diferencia, se ve que se ha quitado el elemento en la secuencia `0`, por lo que genera el *script de edición* trivial siguiente:

* Quite el primer nodo de texto.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problema con la generación de números de secuencia mediante programación

Imagine que se ha escrito la siguiente lógica del generador de árboles de representación:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Ahora, la primera salida es la siguiente:

| Secuencia | Tipo      | Datos   |
| :------: | --------- | :----: |
| 0        | Nodo de texto | First  |
| 1        | Nodo de texto | Second |

Este resultado es idéntico al del caso anterior, por lo que no existen incidencias negativas. `someFlag` es `false` en la segunda representación y la salida es la siguiente:

| Secuencia | Tipo      | Datos   |
| :------: | --------- | ------ |
| 0        | Nodo de texto | Second |

Esta vez, el algoritmo de diferencia observa que se han producido *dos* cambios y genera el siguiente script de edición:

* Cambie el valor del primer nodo de texto a `Second`.
* Quite el segundo nodo de texto.

La generación de los números de secuencia ha perdido toda la información útil sobre el lugar en el que las ramas `if/else` y los bucles estaban presentes en el código original. Esto da como resultado una diferencia **dos veces más larga** que antes.

Este es un ejemplo trivial. En casos más realistas con estructuras complejas y profundamente anidadas, y especialmente con bucles, el costo de rendimiento suele ser mayor. En lugar de identificar inmediatamente los bloques o ramas de bucle que se han insertado o quitado, el algoritmo de diferencia tiene que recorrer profundamente los árboles de representación. Esto suele dar lugar a tener que compilar scripts de edición más largos, ya que el algoritmo de diferencia está desinformado sobre cómo se relacionan entre sí las estructuras antiguas y nuevas.

### <a name="guidance-and-conclusions"></a>Guía y conclusiones

* El rendimiento de la aplicación se ve afectado si los números de secuencia se generan dinámicamente.
* El marco no puede crear sus propios números de secuencia automáticamente en tiempo de ejecución porque la información necesaria no existe a menos que se capture en tiempo de compilación.
* No escriba bloques grandes de lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> implementada de forma manual. Dé preferencia a los archivos `.razor` y permita que el compilador trate los números de secuencia. Si no puede evitar la lógica <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> manual, divida bloques grandes de código en fragmentos más pequeños encapsulados en llamadas <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>. Cada región tiene su propio espacio independiente de números de secuencia, por lo que puede reiniciar desde cero (o cualquier otro número arbitrario) en cada región.
* Si los números de secuencia están codificados, el algoritmo de diferencia solo requiere que los números de secuencia aumenten de valor. El valor inicial y los intervalos son irrelevantes. Una opción legítima es usar el número de línea de código como el número de secuencia, o comenzar a partir de cero y aumentar en unos o cientos (o cualquier intervalo preferido). 
* Blazor utiliza los números de secuencia, mientras que otros marcos de la interfaz de usuario de diferencia de árboles no. La comparación es mucho más rápida cuando se utilizan números de secuencia, y la ventaja de Blazor es un paso de compilación que trata los números de secuencia automáticamente para desarrolladores que crean archivos `.razor`.

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a>Realización de transferencias de datos grandes en aplicaciones Blazor Server

En algunos escenarios, se deben transferir cantidades grandes de datos entre JavaScript y Blazor. Normalmente, se producen transferencias de datos grandes cuando:

* Las API del sistema de archivos del explorador se usan para cargar o descargar un archivo.
* Se requiere la interoperabilidad con una biblioteca de terceros.

En Blazor Server, existe una limitación para evitar el paso de mensajes únicos de gran tamaño que pueden dar lugar a incidencias de rendimiento.

Tenga en cuenta la guía siguiente al desarrollar código que transfiera datos entre JavaScript y Blazor:

* Segmente los datos en partes más pequeñas y envíe los segmentos de datos secuencialmente hasta que el servidor reciba todos los datos.
* No asigne objetos grandes en código JavaScript y C#.
* No bloquee el subproceso de interfaz de usuario principal durante períodos largos al enviar o recibir datos.
* Libere la memoria consumida al completar o cancelar el proceso.
* Aplique los requisitos adicionales siguientes por motivos de seguridad:
  * Declare el tamaño máximo del archivo o los datos que se pueden pasar.
  * Declare la tasa mínima de carga desde el cliente al servidor.
* Después de que el servidor reciba los datos, los datos se pueden:
  * Almacenar temporalmente en un búfer de memoria hasta que se recopilen todos los segmentos.
  * Consumir inmediatamente. Por ejemplo, los datos se pueden almacenar inmediatamente en una base de datos o escribir en el disco a medida que se reciba cada segmento.

La clase de cargador de archivos siguiente controla la interoperabilidad de JS con el cliente. La clase de cargador usa la interoperabilidad de JS para realizar lo siguiente:

* Sondear al cliente para enviar un segmento de datos.
* Anular la transacción si se agota el tiempo de espera del sondeo.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

En el ejemplo anterior:

* `maxBase64SegmentSize` se establece en `8192`, que se calcula a partir de `maxBase64SegmentSize = segmentSize * 4 / 3`.
* Las API de administración de memoria de .NET Core de bajo nivel se usan para almacenar los segmentos de memoria en el servidor en `uploadedSegments`.
* Se usa un método `ReceiveFile` para administrar la carga a través de la interoperabilidad de JS:
  * El tamaño del archivo se determina en bytes a través de la interoperabilidad de JS con `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * El número de segmentos que se van a recibir se calcula y almacena en `numberOfSegments`.
  * Los segmentos se solicitan en un bucle `for` a través de la interoperabilidad de JS con `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`. Todos los segmentos, excepto el último, deben tener 8192 bytes antes de la descodificación. El cliente se ve obligado a enviar los datos de manera eficaz.
  * Para cada segmento recibido, se realizan comprobaciones antes de la descodificación con <xref:System.Convert.TryFromBase64String%2A>.
  * Se devuelve una secuencia con los datos como un elemento <xref:System.IO.Stream> nuevo (`SegmentedStream`) cuando se completa la carga.

La clase de la secuencia segmentada expone la lista de segmentos como un elemento <xref:System.IO.Stream> de solo lectura que no se puede buscar:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

El código siguiente implementa las funciones de JavaScript para recibir los datos:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
