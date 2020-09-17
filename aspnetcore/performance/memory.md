---
title: Administración de memoria y patrones en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se administra la memoria en ASP.NET Core y cómo funciona el recolector de elementos no utilizados (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 7f1d20687f6dd588e125acf3815815c2bcf0cd04
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722688"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Administración de memoria y recolección de elementos no utilizados (GC) en ASP.NET Core

Por [Sébastien Ros](https://github.com/sebastienros) y [Rick Anderson](https://twitter.com/RickAndMSFT)

La administración de memoria es compleja, incluso en un marco administrado como .NET. Analizar y comprender los problemas de memoria puede ser desafiante. En este artículo:

* Estaba motivada por muchos problemas de *pérdida de memoria* y el *GC no funciona* . La mayoría de estos problemas se debieron a no comprender cómo funciona el consumo de memoria en .NET Core o no comprender cómo se mide.
* Muestra el uso de memoria problemático y sugiere enfoques alternativos.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Cómo funciona la recolección de elementos no utilizados (GC) en .NET Core

El GC asigna segmentos del montón donde cada segmento es un intervalo de memoria contiguo. Los objetos colocados en el montón se clasifican en una de estas tres generaciones: 0, 1 o 2. La generación determina la frecuencia con la que el GC intenta liberar memoria en los objetos administrados a los que la aplicación ya no hace referencia. Las generaciones con números inferiores son GC con más frecuencia.

Los objetos se mueven de una generación a otra en función de su duración. A medida que los objetos viven más tiempo, se mueven a una generación superior. Como se mencionó anteriormente, las generaciones mayores son menos a menudo. Los objetos de corta duración siempre permanecen en la generación 0. Por ejemplo, los objetos a los que se hace referencia durante la vida de una solicitud Web son de corta duración. Los [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) de nivel de aplicación generalmente migran a la generación 2.

Cuando se inicia una aplicación ASP.NET Core, el GC:

* Reserva memoria para los segmentos de montón iniciales.
* Confirma una pequeña parte de la memoria cuando se carga el tiempo de ejecución.

Las asignaciones de memoria anteriores se realizan por motivos de rendimiento. La ventaja de rendimiento procede de los segmentos del montón en la memoria contigua.

### <a name="call-gccollect"></a>Llame a GC. Impuesto

Llamando a [GC. Recopilar](xref:System.GC.Collect*) explícitamente:

* **No** debe realizarse en las aplicaciones de ASP.net Core de producción.
* Resulta útil para investigar pérdidas de memoria.
* Al investigar, comprueba que el GC ha quitado todos los objetos pendientes de la memoria, por lo que se puede medir la memoria.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analizar el uso de memoria de una aplicación

Las herramientas dedicadas pueden ayudar a analizar el uso de memoria:

- Recuento de referencias de objeto
- Medir la cantidad de impacto que el GC tiene en el uso de CPU
- Medir el espacio de memoria usado para cada generación

Use las siguientes herramientas para analizar el uso de memoria:

* [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): puede usarse en equipos de producción.
* [Analizar el uso de memoria sin el depurador de Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Análisis del uso de memoria en Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Detección de problemas de memoria

Se puede usar el administrador de tareas para hacerse una idea de la cantidad de memoria que está usando una aplicación de ASP.NET. El valor de memoria del administrador de tareas:

* Representa la cantidad de memoria que utiliza el proceso ASP.NET.
* Incluye los objetos vivos de la aplicación y otros consumidores de memoria, como el uso de memoria nativa.

Si el valor de memoria del administrador de tareas aumenta indefinidamente y nunca se reduce, la aplicación tiene una pérdida de memoria. En las secciones siguientes se muestran y explican varios patrones de uso de memoria.

## <a name="sample-display-memory-usage-app"></a>Ejemplo de aplicación de uso de memoria de muestra

La [aplicación de ejemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponible en github. La aplicación MemoryLeak:

* Incluye un controlador de diagnóstico que recopila datos de GC y memoria en tiempo real de la aplicación.
* Tiene una página de índice que muestra los datos de memoria y GC. La página de índice se actualiza cada segundo.
* Contiene un controlador de API que proporciona varios patrones de carga de memoria.
* No es una herramienta compatible; sin embargo, se puede usar para mostrar patrones de uso de memoria de ASP.NET Core aplicaciones.

Ejecute MemoryLeak. La memoria asignada aumenta lentamente hasta que se produce un GC. La memoria aumenta porque la herramienta asigna el objeto personalizado para capturar los datos. La siguiente imagen muestra la página de índice de MemoryLeak cuando se produce un GC de gen. 0. El gráfico muestra 0 RPS (solicitudes por segundo) porque no se ha llamado a ningún punto de conexión de API del controlador de API.

![gráfico anterior](memory/_static/0RPS.png)

El gráfico muestra dos valores para el uso de memoria:

- Asignado: la cantidad de memoria ocupada por los objetos administrados
- Espacio de [trabajo](/windows/win32/memory/working-set): el conjunto de páginas del espacio de direcciones virtuales del proceso residente actualmente en la memoria física. El espacio de trabajo mostrado es el mismo valor que se muestra en el administrador de tareas.

### <a name="transient-objects"></a>Objetos transitorios

La API siguiente crea una instancia de cadena de 10 KB y la devuelve al cliente. En cada solicitud, se asigna un nuevo objeto en la memoria y se escribe en la respuesta. Las cadenas se almacenan como caracteres UTF-16 en .NET, por lo que cada carácter ocupa 2 bytes en la memoria.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

El siguiente gráfico se genera con una carga relativamente pequeña en para mostrar cómo las asignaciones de memoria se ven afectadas por el GC.

![gráfico anterior](memory/_static/bigstring.png)

En el gráfico anterior se muestra:

* 4K RPS (solicitudes por segundo).
* Las colecciones de GC de generación 0 se producen aproximadamente cada dos segundos.
* El espacio de trabajo es constante de aproximadamente 500 MB.
* La CPU es del 12%.
* El consumo de memoria y la versión (a través de GC) son estables.

El siguiente gráfico se toma en el rendimiento máximo que puede controlar el equipo.

![gráfico anterior](memory/_static/bigstring2.png)

En el gráfico anterior se muestra:

* 22K RPS
* Las colecciones de GC de generación 0 se producen varias veces por segundo.
* Las colecciones de la generación 1 se desencadenan porque la aplicación ha asignado significativamente más memoria por segundo.
* El espacio de trabajo es constante de aproximadamente 500 MB.
* La CPU es del 33%.
* El consumo de memoria y la versión (a través de GC) son estables.
* CPU (33%) no se ha utilizado en exceso, por lo que la recolección de elementos no utilizados puede mantener un gran número de asignaciones.

### <a name="workstation-gc-vs-server-gc"></a>GC de estación de trabajo frente a GC de servidor

El recolector de elementos no utilizados de .NET tiene dos modos diferentes:

* **GC de estación de trabajo**: optimizado para el escritorio.
* **GC del servidor**. El GC predeterminado para ASP.NET Core aplicaciones. Optimizado para el servidor.

El modo GC se puede establecer explícitamente en el archivo de proyecto o en el *runtimeconfig.jsen* el archivo de la aplicación publicada. El marcado siguiente muestra `ServerGarbageCollection` el valor en el archivo de proyecto:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Cambiar `ServerGarbageCollection` en el archivo de proyecto requiere que se vuelva a generar la aplicación.

**Nota:** La recolección de elementos **no** utilizados de servidor no está disponible en equipos con un solo núcleo. Para obtener más información, vea <xref:System.Runtime.GCSettings.IsServerGC>.

En la imagen siguiente se muestra el perfil de memoria en un 5.000 RPS mediante el GC de la estación de trabajo.

![gráfico anterior](memory/_static/workstation.png)

Las diferencias entre este gráfico y la versión del servidor son significativas:

- El espacio de trabajo desciende de 500 MB a 70 MB.
- El GC realiza colecciones de la generación 0 varias veces por segundo en lugar de cada dos segundos.
- GC desciende de 300 MB a 10 MB.

En un entorno de servidor Web típico, el uso de CPU es más importante que la memoria, por lo que el GC del servidor es mejor. Si la utilización de la memoria es alta y el uso de la CPU es relativamente bajo, el GC de la estación de trabajo puede ser más eficaz. Por ejemplo, alta densidad que hospeda varias aplicaciones web en las que la memoria es escasa.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC mediante Docker y contenedores pequeños

Cuando varias aplicaciones en contenedor se ejecutan en un equipo, la GC de la estación de trabajo podría tener un mayor rendimiento que el GC del servidor. Para obtener más información, vea [ejecutar con el GC del servidor en un contenedor pequeño](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) y [ejecutar con el GC del servidor en un pequeño escenario de contenedor, parte 1: límite máximo para el montón de GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Referencias de objeto persistentes

El GC no puede liberar objetos a los que se hace referencia. Los objetos a los que se hace referencia pero que ya no se necesitan producen una pérdida de memoria. Si la aplicación asigna objetos con frecuencia y no puede liberarlos después de que ya no se necesiten, el uso de memoria aumentará con el tiempo.

La API siguiente crea una instancia de cadena de 10 KB y la devuelve al cliente. La diferencia con el ejemplo anterior es que un miembro estático hace referencia a esta instancia, lo que significa que nunca está disponible para la recolección.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

El código anterior:

* Es un ejemplo de una pérdida de memoria típica.
* Con llamadas frecuentes, hace que la memoria de la aplicación aumente hasta que el proceso se bloquee con una `OutOfMemory` excepción.

![gráfico anterior](memory/_static/eternal.png)

En la imagen anterior:

* La prueba de carga del `/api/staticstring` punto de conexión provoca un aumento lineal en la memoria.
* El GC intenta liberar memoria a medida que aumenta la presión de memoria, llamando a una colección de generación 2.
* El GC no puede liberar la memoria perdida. La asignación y el espacio de trabajo aumentan con el tiempo.

Algunos escenarios, como el almacenamiento en caché, requieren que las referencias de objeto se mantengan hasta que la presión de memoria obliga a que se liberen. La <xref:System.WeakReference> clase se puede usar para este tipo de código de almacenamiento en caché. Un `WeakReference` objeto se recopila bajo presión de memoria. La implementación predeterminada de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> utiliza `WeakReference` .

### <a name="native-memory"></a>Memoria nativa

Algunos objetos .NET Core se basan en la memoria nativa. El GC **no** puede recopilar memoria nativa. El objeto .NET que usa memoria nativa debe liberarlo mediante código nativo.

.NET proporciona la <xref:System.IDisposable> interfaz para permitir que los desarrolladores liberen memoria nativa. Incluso si <xref:System.IDisposable.Dispose*> no se llama a, las clases implementadas correctamente llamarán `Dispose` cuando se ejecute el [finalizador](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .

Observe el código siguiente:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) es una clase administrada, por lo que cualquier instancia se recopilará al final de la solicitud.

En la imagen siguiente se muestra el perfil de memoria al invocar la `fileprovider` API continuamente.

![gráfico anterior](memory/_static/fileprovider.png)

En el gráfico anterior se muestra un problema obvio con la implementación de esta clase, ya que sigue aumentando el uso de memoria. Se trata de un problema conocido del que se está realizando [el seguimiento en este problema](https://github.com/dotnet/aspnetcore/issues/3110).

La misma fuga podría producirse en el código de usuario, mediante una de las siguientes acciones:

* No se libera correctamente la clase.
* Se olvidará de invocar el `Dispose` método de los objetos dependientes que se deben desechar.

### <a name="large-objects-heap"></a>Montón de objetos grandes

Los ciclos gratuitos y de asignación de memoria frecuentes pueden fragmentar la memoria, especialmente cuando se asignan grandes fragmentos de memoria. Los objetos se asignan en bloques de memoria contiguos. Para mitigar la fragmentación, cuando el GC libera memoria, intenta desfragmentarla. Este proceso se denomina **compactación**. La compactación implica mover objetos. Mover objetos grandes impone una reducción del rendimiento. Por esta razón, el GC crea una zona de memoria especial para objetos _grandes_ , denominada [montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap) (montón). Los objetos que tienen más de 85.000 bytes (aproximadamente 83 KB) son:

* Se coloca en el montón.
* No compactado.
* Recopilados durante la generación 2 GC.

Cuando el montón está lleno, el GC desencadenará una recolección de la generación 2. Recopilaciones de generación 2:

* Son intrínsecamente lentas.
* Además, incurre en el costo de desencadenar una colección en todas las demás generaciones.

El siguiente código compacta el montón inmediatamente:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Vea <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> para obtener información sobre cómo compactar el montón de datos.

En los contenedores con .NET Core 3,0 y versiones posteriores, el montón de base de los se compacta automáticamente.

La siguiente API que muestra este comportamiento:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

En el gráfico siguiente se muestra el perfil de memoria de la llamada al `/api/loh/84975` punto de conexión, en la carga máxima:

![gráfico anterior](memory/_static/loh1.png)

En el gráfico siguiente se muestra el perfil de memoria de llamar al `/api/loh/84976` extremo, asignando *solo un byte más*:

![gráfico anterior](memory/_static/loh2.png)

Nota: la `byte[]` estructura tiene bytes de sobrecarga. Por eso 84.976 bytes desencadena el límite de 85.000.

Comparar los dos gráficos anteriores:

* El espacio de trabajo es similar en ambos escenarios, aproximadamente 450 MB.
* El bajo solicitudes de montón (84.975 bytes) muestra principalmente colecciones de la generación 0.
* Las solicitudes de montón superior generan recolecciones de la generación 2. Las colecciones de generación 2 son costosas. Se necesita más CPU y el rendimiento se reduce casi el 50%.

Los objetos temporales grandes son especialmente problemáticos, ya que provocan GC de la segunda generación.

Para obtener el máximo rendimiento, se debe minimizar el uso de objetos grandes. Si es posible, divida los objetos grandes. Por ejemplo, el middleware de [almacenamiento en caché de respuesta](xref:performance/caching/response) de ASP.net Core divide las entradas de la memoria caché en bloques inferiores a 85.000 bytes.

En los siguientes vínculos se muestra el enfoque ASP.NET Core para mantener objetos bajo el límite de montón:

* [ResponseCaching/streams/StreamUtilities. CS](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. CS](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Para más información, consulte:

* [Montón de objetos grandes descubierto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

El uso incorrecto <xref:System.Net.Http.HttpClient> de puede dar lugar a una pérdida de recursos. Recursos del sistema, como conexiones de bases de datos, sockets, identificadores de archivo, etc.:

* Son más escasos que la memoria.
* Son más problemáticas cuando se pierden la memoria.

Los desarrolladores de .NET experimentados saben llamar a <xref:System.IDisposable.Dispose*> en objetos que implementan <xref:System.IDisposable> . Si no se desechan los objetos que implementan `IDisposable` normalmente se produce una pérdida de memoria o de recursos del sistema perdidos.

`HttpClient` implementa `IDisposable` , pero **no** debe desecharse en cada invocación. En su lugar, debe volver a `HttpClient` usarse.

El siguiente punto de conexión crea y desecha una nueva  `HttpClient` instancia en cada solicitud:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Bajo carga, se registran los siguientes mensajes de error:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Aunque `HttpClient` se eliminen las instancias, la conexión de red real tarda algún tiempo en publicarse en el sistema operativo. Al crear continuamente nuevas conexiones, se produce el agotamiento de los _puertos_ . Cada conexión de cliente requiere su propio puerto de cliente.

Una manera de evitar el agotamiento del puerto es reutilizar la misma `HttpClient` instancia:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

La `HttpClient` instancia se libera cuando se detiene la aplicación. Este ejemplo muestra que no se deben eliminar todos los recursos descartados después de cada uso.

Vea lo siguiente para obtener una mejor manera de controlar la duración de una `HttpClient` instancia de:

* [HttpClient y administración de la duración](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [Blog de factoría de HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Agrupación de objetos

En el ejemplo anterior se mostró cómo `HttpClient` se puede hacer que la instancia sea estática y reutilizada por todas las solicitudes. La reutilización evita quedarse sin recursos.

Agrupación de objetos:

* Usa el patrón de reutilización.
* Está diseñado para objetos que son caros de crear.

Un grupo es una colección de objetos previamente inicializados que se pueden reservar y liberar entre subprocesos. Los grupos pueden definir reglas de asignación como límites, tamaños predefinidos o tasa de crecimiento.

El paquete NuGet [Microsoft. Extensions. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contiene clases que ayudan a administrar estos grupos.

El siguiente punto de conexión de API crea una instancia de un `byte` búfer que se rellena con números aleatorios en cada solicitud:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

En el siguiente gráfico se muestra la llamada a la API anterior con carga moderada:

![gráfico anterior](memory/_static/array.png)

En el gráfico anterior, las recopilaciones de generación 0 se producen aproximadamente una vez por segundo.

El código anterior se puede optimizar agrupando el `byte` búfer mediante [ArrayPool \<T> ](xref:System.Buffers.ArrayPool`1). Una instancia estática se reutiliza en todas las solicitudes.

Lo que es diferente con este enfoque es que la API devuelve un objeto agrupado. Esto significa:

* El objeto está fuera del control tan pronto como se devuelve desde el método.
* No se puede liberar el objeto.

Para configurar la eliminación del objeto:

* Encapsula la matriz agrupada en un objeto descartable.
* Registre el objeto agrupado con [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` se encargará de llamar a `Dispose` en el objeto de destino para que solo se libere cuando se complete la solicitud HTTP.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Al aplicar la misma carga que la versión no agrupada, se obtiene el siguiente gráfico:

![gráfico anterior](memory/_static/pooledarray.png)

La diferencia principal son los bytes asignados y, como consecuencia, muchas menos de la generación 0.

## <a name="additional-resources"></a>Recursos adicionales

* [Recolección de elementos no utilizados](/dotnet/standard/garbage-collection/)
* [Descripción de los distintos modos de GC con el visualizador de simultaneidad](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Montón de objetos grandes descubierto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap)