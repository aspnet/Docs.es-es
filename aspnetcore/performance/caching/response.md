---
title: Almacenamiento en caché de respuestas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar el almacenamiento en caché de respuestas para reducir los requisitos de ancho de banda y aumentar el rendimiento de las aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 2864de5b9931ed255569cb087c67c71004c4df92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059018"
---
# <a name="response-caching-in-aspnet-core"></a>Almacenamiento en caché de respuestas en ASP.NET Core

Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)y [Steve Smith](https://ardalis.com/)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

El almacenamiento en caché de respuestas reduce el número de solicitudes que un cliente o proxy realiza a un servidor Web. El almacenamiento en caché de respuestas también reduce la cantidad de trabajo que realiza el servidor web para generar una respuesta. El almacenamiento en caché de respuestas está controlado por encabezados que especifican cómo desea que el cliente, el proxy y el middleware almacenen en caché las respuestas.

El [atributo ResponseCache](#responsecache-attribute) participa en el establecimiento de encabezados de caché de respuesta. Los clientes y los servidores proxy intermedios deben respetar los encabezados para almacenar en caché las respuestas en la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Para el almacenamiento en caché del lado servidor que sigue la especificación HTTP 1,1 Caching, utilice el [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware). El middleware puede usar las <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> propiedades para influir en el comportamiento del almacenamiento en caché del servidor.

## <a name="http-based-response-caching"></a>Almacenamiento en caché de respuesta basada en HTTP

La [especificación HTTP 1,1 Caching](https://tools.ietf.org/html/rfc7234) describe el comportamiento de las memorias caché de Internet. El encabezado HTTP principal usado para el almacenamiento en caché es [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), que se usa para especificar *directivas* de caché. Las directivas controlan el comportamiento de almacenamiento en caché a medida que las solicitudes proceden de los clientes a los servidores y, a medida que las respuestas, vuelven desde los servidores a los clientes. Las solicitudes y respuestas se desplazan a través de servidores proxy y los servidores proxy también deben cumplir con la especificación de almacenamiento en caché HTTP 1,1.

`Cache-Control`En la tabla siguiente se muestran las directivas comunes.

| Directiva                                                       | Acción |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Una memoria caché puede almacenar la respuesta. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | La respuesta no debe almacenarse en una memoria caché compartida. Una caché privada puede almacenar y reutilizar la respuesta. |
| [Max-Age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | El cliente no acepta una respuesta cuya antigüedad sea mayor que el número de segundos especificado. Ejemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mes) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **En las solicitudes** : una memoria caché no debe usar una respuesta almacenada para satisfacer la solicitud. El servidor de origen regenera la respuesta para el cliente y el middleware actualiza la respuesta almacenada en su caché.<br><br>**En las respuestas** : la respuesta no se debe utilizar para una solicitud subsiguiente sin validación en el servidor de origen. |
| [sin almacén](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **En las solicitudes** : una memoria caché no debe almacenar la solicitud.<br><br>**En las respuestas** : una memoria caché no debe almacenar ninguna parte de la respuesta. |

En la tabla siguiente se muestran otros encabezados de caché que desempeñan un rol en el almacenamiento en caché.

| Encabezado                                                     | Función |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1): edad     | Una estimación de la cantidad de tiempo en segundos transcurrido desde que se generó la respuesta o se validó correctamente en el servidor de origen. |
| [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | Hora a partir de la cual la respuesta se considera obsoleta. |
| [Omiti](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe por compatibilidad con versiones anteriores de caché HTTP/1.0 para establecer el `no-cache` comportamiento. Si el `Cache-Control` encabezado está presente, `Pragma` se omite el encabezado. |
| [Variaciones](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Especifica que no se debe enviar una respuesta almacenada en caché a menos que todos los `Vary` campos de encabezado coincidan en la solicitud original de la respuesta almacenada en caché y la nueva solicitud. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>El almacenamiento en caché basado en HTTP respeta las directivas de Cache-Control de solicitudes

La [especificación de almacenamiento en caché HTTP 1,1 para el encabezado Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requiere una memoria caché para respetar un `Cache-Control` encabezado válido enviado por el cliente. Un cliente puede realizar solicitudes con un `no-cache` valor de encabezado y obligar al servidor a generar una nueva respuesta para cada solicitud.

`Cache-Control`Tener en cuenta siempre los encabezados de solicitud de cliente tiene sentido si tiene en cuenta el objetivo del almacenamiento en caché de http. En la especificación oficial, el almacenamiento en caché está pensado para reducir la latencia y la sobrecarga de red que supone satisfacer las solicitudes a través de una red de clientes, servidores proxy y servidores. No es necesariamente una manera de controlar la carga en un servidor de origen.

No hay ningún control de desarrollador sobre este comportamiento de almacenamiento en caché cuando se usa el [middleware de almacenamiento en caché de respuesta](xref:performance/caching/middleware) , ya que el middleware se adhiere a la especificación oficial de almacenamiento en caché. [Las mejoras planeadas en el middleware son la](https://github.com/dotnet/AspNetCore/issues/2612) oportunidad de configurar el middleware para omitir el encabezado de una solicitud `Cache-Control` al decidir si se va a dar servicio a una respuesta almacenada en caché. Las mejoras planeadas proporcionan una oportunidad para controlar mejor la carga del servidor.

## <a name="other-caching-technology-in-aspnet-core"></a>Otra tecnología de almacenamiento en caché en ASP.NET Core

### <a name="in-memory-caching"></a>Almacenamiento en caché en memoria

El almacenamiento en caché en memoria utiliza la memoria del servidor para almacenar los datos en caché. Este tipo de almacenamiento en caché es adecuado para un solo servidor o para varios servidores que usan *sesiones permanentes* . Las sesiones permanentes significan que las solicitudes de un cliente siempre se enrutan al mismo servidor para su procesamiento.

Para obtener más información, vea <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Caché distribuida

Use una caché distribuida para almacenar los datos en memoria cuando la aplicación se hospeda en una granja de servidores o en la nube. La memoria caché se comparte entre los servidores que procesan las solicitudes. Un cliente puede enviar una solicitud controlada por cualquier servidor del grupo si están disponibles los datos almacenados en caché del cliente. ASP.NET Core funciona con memorias caché distribuidas SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)y [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Para obtener más información, vea <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Asistente de etiquetas de caché

Almacenar en caché el contenido de una vista o una página de MVC Razor con la aplicación auxiliar de etiquetas de caché. La aplicación auxiliar de etiquetas de caché usa el almacenamiento en caché en memoria para almacenar los datos.

Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Asistente de etiquetas de caché distribuida

Almacene en caché el contenido de una vista o una Razor Página de MVC en escenarios de granja de servidores web o nube distribuida con la aplicación auxiliar de etiquetas de caché distribuida. La aplicación auxiliar de etiquetas de caché distribuida usa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)o [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) para almacenar los datos.

Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Atributo ResponseCache

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Especifica los parámetros necesarios para establecer los encabezados adecuados en el almacenamiento en caché de respuestas.

> [!WARNING]
> Deshabilite el almacenamiento en caché para el contenido que contiene información de los clientes autenticados. El almacenamiento en caché solo debe estar habilitado para el contenido que no cambia en función de la identidad de un usuario o de si un usuario ha iniciado sesión.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> cambia la respuesta almacenada por los valores de la lista especificada de claves de consulta. Cuando se proporciona un valor único de `*` , el middleware varía las respuestas de todos los parámetros de la cadena de consulta de solicitud.

El [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) debe estar habilitado para establecer la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propiedad. De lo contrario, se produce una excepción en tiempo de ejecución. No hay un encabezado HTTP correspondiente para la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propiedad. La propiedad es una característica de HTTP administrada por middleware de almacenamiento en caché de respuestas. Para que el middleware atienda una respuesta almacenada en caché, la cadena de consulta y el valor de la cadena de consulta deben coincidir con una solicitud anterior. Por ejemplo, considere la secuencia de solicitudes y los resultados que se muestran en la tabla siguiente.

| Solicitud                          | Resultado                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Se devuelve desde el servidor. |
| `http://example.com?key1=value1` | Se devuelve desde middleware. |
| `http://example.com?key1=value2` | Se devuelve desde el servidor. |

El servidor devuelve la primera solicitud y la almacena en caché en middleware. El middleware devuelve la segunda solicitud porque la cadena de consulta coincide con la solicitud anterior. La tercera solicitud no está en la caché de middleware porque el valor de la cadena de consulta no coincide con una solicitud anterior.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Se utiliza para configurar y crear (mediante <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) un `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . El `ResponseCacheFilter` realiza el trabajo de actualizar los encabezados HTTP y las características correspondientes de la respuesta. El filtro:

* Quita todos los encabezados existentes para `Vary` , `Cache-Control` y `Pragma` .
* Escribe los encabezados adecuados en función de las propiedades establecidas en <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .
* Actualiza la característica HTTP de almacenamiento en caché de respuestas si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> está establecida.

### <a name="vary"></a>Variaciones

Este encabezado solo se escribe cuando <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> se establece la propiedad. Propiedad establecida en el `Vary` valor de la propiedad. En el ejemplo siguiente se usa la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propiedad:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Use la aplicación de ejemplo para ver los encabezados de respuesta con las herramientas de red del explorador. Los siguientes encabezados de respuesta se envían con la respuesta de la página Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>Nostore y Location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> invalida la mayoría de las demás propiedades. Cuando esta propiedad se establece en `true` , el `Cache-Control` encabezado se establece en `no-store` . Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> está establecido en `None` :

* El valor de `Cache-Control` está establecido en `no-store,no-cache`.
* El valor de `Pragma` está establecido en `no-cache`.

Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> es `false` y <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> es `None` , `Cache-Control` y `Pragma` se establecen en `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> normalmente se establece en `true` para las páginas de error. La página Cache2 de la aplicación de ejemplo genera encabezados de respuesta que indican al cliente que no almacene la respuesta.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

La aplicación de ejemplo devuelve la página Cache2 con los encabezados siguientes:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Ubicación y duración

Para habilitar el almacenamiento en caché, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> debe establecerse en un valor positivo y <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> debe ser `Any` (el valor predeterminado) o `Client` . El marco de trabajo establece el `Cache-Control` encabezado en el valor de ubicación seguido del `max-age` de la respuesta.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>las opciones de `Any` y `Client` traducen en `Cache-Control` valores de encabezado de `public` y `private` , respectivamente. Como se indicó en la sección [nostore y Location. None](#nostore-and-locationnone) , si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> se establece en, `None` los `Cache-Control` encabezados y se establecen en `Pragma` `no-cache` .

`Location.Any` ( `Cache-Control` establecido en `public` ) indica que el *cliente o cualquier proxy intermedio* puede almacenar en caché el valor, incluido el [middleware de almacenamiento en](xref:performance/caching/middleware)caché de las respuestas.

`Location.Client` ( `Cache-Control` establecido en `private` ) indica que *solo el cliente* puede almacenar en caché el valor. Ninguna caché intermedia debe almacenar en caché el valor, incluido el [middleware de almacenamiento en](xref:performance/caching/middleware)caché de las respuestas.

Los encabezados de control de caché simplemente proporcionan instrucciones a los clientes y los proxies intermediarios cuando y cómo almacenar en caché las respuestas. No hay ninguna garantía de que los clientes y los servidores proxy cumplan la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234). El [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) sigue siempre las reglas de almacenamiento en caché establecidas por la especificación.

En el ejemplo siguiente se muestra el modelo de página Cache3 de la aplicación de ejemplo y los encabezados generados estableciendo <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> y saliendo del <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valor predeterminado:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

La aplicación de ejemplo devuelve la página Cache3 con el siguiente encabezado:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Perfiles de caché

En lugar de duplicar la configuración de la caché de respuestas en muchos atributos de acción del controlador, los perfiles de caché se pueden configurar como opciones al configurar MVC/ Razor páginas en `Startup.ConfigureServices` . Los valores que se encuentran en un perfil de caché al que se hace referencia se utilizan como valores predeterminados de <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> y se reemplazan por las propiedades especificadas en el atributo.

Configure un perfil de caché. En el ejemplo siguiente se muestra un perfil de caché de 30 segundos en la aplicación de ejemplo `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

El modelo de página Cache4 de la aplicación de ejemplo hace referencia al `Default30` Perfil de caché:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Se puede aplicar a:

* Razor Páginas: no se pueden aplicar atributos a los métodos de controlador.
* Controladores MVC.
* Métodos de acción de MVC: los atributos de nivel de método invalidan los valores especificados en atributos de nivel de clase.

El encabezado resultante aplicado a la respuesta de la página Cache4 por el `Default30` Perfil de caché:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Recursos adicionales

* [Almacenar respuestas en cachés](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
