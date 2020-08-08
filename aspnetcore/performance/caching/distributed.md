---
title: Almacenamiento en caché distribuido en ASP.NET Core
author: rick-anderson
description: Aprenda a usar una ASP.NET Core caché distribuida para mejorar el rendimiento y la escalabilidad de las aplicaciones, especialmente en un entorno de granja de servidores o en la nube.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 64a4b6f606a4f5f8e73ef08f53cbb6e4003245aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020683"
---
# <a name="distributed-caching-in-aspnet-core"></a>Almacenamiento en caché distribuido en ASP.NET Core

Por [Mohsin Nasir](https://github.com/mohsinnasir) y [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Una caché distribuida es una memoria caché compartida por varios servidores de aplicaciones, que normalmente se mantiene como un servicio externo a los servidores de aplicaciones que tienen acceso a ella. Una caché distribuida puede mejorar el rendimiento y la escalabilidad de una aplicación ASP.NET Core, especialmente cuando la aplicación está hospedada en un servicio en la nube o en una granja de servidores.

Una caché distribuida tiene varias ventajas con respecto a otros escenarios de almacenamiento en caché en los que los datos almacenados en caché se almacenan en servidores de aplicaciones individuales.

Cuando se distribuyen los datos almacenados en caché, los datos:

* Es *coherente (coherente)* entre las solicitudes a varios servidores.
* Sobrevive los reinicios del servidor y las implementaciones de aplicaciones.
* No usa la memoria local.

La configuración de caché distribuida es específica de la implementación. En este artículo se describe cómo configurar SQL Server y las cachés distribuidas de Redis. También hay implementaciones de terceros disponibles, como [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache)). Independientemente de la implementación seleccionada, la aplicación interactúa con la memoria caché mediante la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Para usar una SQL Server caché distribuida, agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar una caché distribuida de Redis, agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

Para usar caché distribuida de NCache, agregue una referencia de paquete al paquete [NCache. Microsoft. Extensions. Caching. código abierto](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .

## <a name="idistributedcache-interface"></a>Interfaz IDistributedCache

La <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz proporciona los métodos siguientes para manipular los elementos de la implementación de caché distribuida:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Acepta una clave de cadena y recupera un elemento almacenado en memoria caché como una `byte[]` matriz, si se encuentra en la memoria caché.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Agrega un elemento (como `byte[]` matriz) a la memoria caché utilizando una clave de cadena.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualiza un elemento en la memoria caché en función de su clave, restableciendo el tiempo de espera de expiración variable (si existe).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Quita un elemento de la memoria caché en función de su clave de cadena.

## <a name="establish-distributed-caching-services"></a>Establecer servicios de almacenamiento en caché distribuidos

Registre una implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en `Startup.ConfigureServices` . Las implementaciones proporcionadas por el marco de trabajo que se describen en este tema incluyen:

* [Caché de memoria distribuida](#distributed-memory-cache)
* [Caché de SQL Server distribuida](#distributed-sql-server-cache)
* [Caché en Redis distribuida](#distributed-redis-cache)
* [Caché de NCache distribuida](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Caché de memoria distribuida

La memoria caché de memoria distribuida ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) es una implementación proporcionada por el marco de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que almacena elementos en la memoria. La caché de memoria distribuida no es una caché distribuida real. Los elementos almacenados en caché se almacenan en la instancia de la aplicación en el servidor donde se ejecuta la aplicación.

La caché de memoria distribuida es una implementación útil:

* En escenarios de desarrollo y pruebas.
* Cuando se usa un solo servidor en producción y el consumo de memoria no es un problema. La implementación de la caché de memoria distribuida abstrae el almacenamiento de datos en caché. Permite implementar una solución de almacenamiento en caché distribuida verdadera en el futuro si se necesitan varios nodos o tolerancia a errores.

La aplicación de ejemplo hace uso de la caché de memoria distribuida cuando la aplicación se ejecuta en el entorno de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Caché de SQL Server distribuida

La implementación de la memoria caché de SQL Server distribuida ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que la memoria caché distribuida use una base de datos SQL Server como su memoria auxiliar. Para crear una SQL Server tabla de elementos almacenados en caché en una instancia de SQL Server, puede usar la `sql-cache` herramienta. La herramienta crea una tabla con el nombre y el esquema que especifique.

Cree una tabla en SQL Server ejecutando el `sql-cache create` comando. Proporcione la SQL Server instancia ( `Data Source` ), la base de datos ( `Initial Catalog` ), el esquema (por ejemplo, `dbo` ) y el nombre de la tabla (por ejemplo, `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Se registra un mensaje para indicar que la herramienta se ha realizado correctamente:

```console
Table and index were created successfully.
```

La tabla creada por la `sql-cache` herramienta tiene el siguiente esquema:

![Tabla de caché SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Una aplicación debe manipular los valores de la memoria caché mediante una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , no un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

La aplicación de ejemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> en un entorno que no es de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (y, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> y <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se almacena normalmente fuera del control de código fuente (por ejemplo, lo almacena el [Administrador secreto](xref:security/app-secrets) o en *appsettings.jsen* / *appSettings. { ENVIRONMENT}. JSON* files). La cadena de conexión puede contener credenciales que deben mantenerse fuera de los sistemas de control de código fuente.

### <a name="distributed-redis-cache"></a>Redis Cache distribuido

[Redis](https://redis.io/) es un almacén de datos en memoria de código abierto, que a menudo se usa como caché distribuida. Puede usar Redis localmente, y puede configurar un [Azure Redis cache](https://azure.microsoft.com/services/cache/) para una aplicación de ASP.net Core hospedada en Azure.

Una aplicación configura la implementación de la memoria caché mediante una <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instancia <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> de () en un entorno que no es de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Para instalar Redis en el equipo local:

1. Instale el [paquete chocolatey Redis](https://chocolatey.org/packages/redis-64/).
1. Ejecute `redis-server` desde un símbolo del sistema.

### <a name="distributed-ncache-cache"></a>Caché de NCache distribuida

[NCache](https://github.com/Alachisoft/NCache) es una caché distribuida en memoria de código abierto desarrollada de forma nativa en .NET y .net Core. NCache funciona localmente y se configura como un clúster de caché distribuida para una aplicación ASP.NET Core que se ejecuta en Azure o en otras plataformas de hospedaje.

Para instalar y configurar NCache en el equipo local, consulte [Guía de introducción de NCache para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Para configurar NCache:

1. Instalación de [NuGet de código abierto de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configure el clúster de caché en [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Agregue el siguiente código a `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Usar la memoria caché distribuida

Para usar la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz, solicite una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> desde cualquier constructor de la aplicación. La instancia se proporciona mediante la [inserción de dependencias (di)](xref:fundamentals/dependency-injection).

Cuando se inicia la aplicación de ejemplo, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> se inserta en `Startup.Configure` . La hora actual se almacena en la memoria caché mediante <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (para obtener más información, consulte [host genérico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

La aplicación de ejemplo inyecta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en el `IndexModel` para su uso en la página de índice.

Cada vez que se carga la página de índice, se comprueba la hora almacenada en caché en `OnGetAsync` . Si la hora almacenada en caché no ha expirado, se muestra la hora. Si han transcurrido 20 segundos desde la última vez que se tuvo acceso a la hora almacenada en caché (la última vez que se cargó esta página), la página muestra la *fecha de expiración de la memoria caché*.

Actualice inmediatamente la hora almacenada en caché a la hora actual; para ello, seleccione el botón **restablecer hora de almacenamiento en caché** . El botón desencadena el `OnPostResetCachedTime` método de control.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> No es necesario usar un singleton o una duración de ámbito para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> las instancias (al menos para las implementaciones integradas).
>
> También puede crear una <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instancia siempre que necesite una en lugar de usar di, pero la creación de una instancia de en el código puede hacer que el código sea más difícil de probar e infringe el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recomendaciones

A la hora de decidir qué implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> es la más adecuada para su aplicación, tenga en cuenta lo siguiente:

* Infraestructura existente
* Requisitos de rendimiento
* Coste
* Experiencia del equipo

Las soluciones de almacenamiento en caché suelen basarse en el almacenamiento en memoria para proporcionar una recuperación rápida de los datos almacenados en memoria caché, pero la memoria es un recurso limitado y se amplía de forma costosa. Almacene solo los datos usados habitualmente en una memoria caché.

Por lo general, una caché en Redis proporciona un mayor rendimiento y una menor latencia que una memoria caché de SQL Server. Sin embargo, normalmente es necesario realizar pruebas comparativas para determinar las características de rendimiento de las estrategias de almacenamiento en caché.

Cuando SQL Server se usa como un almacén de respaldo de caché distribuida, el uso de la misma base de datos para la memoria caché y el almacenamiento y la recuperación de datos normales de la aplicación puede afectar negativamente al rendimiento de ambos. Se recomienda usar una instancia de SQL Server dedicada para el almacén de respaldo de caché distribuida.

## <a name="additional-resources"></a>Recursos adicionales

* [Redis Cache en Azure](/azure/azure-cache-for-redis/)
* [SQL Database en Azure](/azure/sql-database/)
* [ASP.net Core del proveedor de IDistributedCache para NCache en granjas de servidores web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Una caché distribuida es una memoria caché compartida por varios servidores de aplicaciones, que normalmente se mantiene como un servicio externo a los servidores de aplicaciones que tienen acceso a ella. Una caché distribuida puede mejorar el rendimiento y la escalabilidad de una aplicación ASP.NET Core, especialmente cuando la aplicación está hospedada en un servicio en la nube o en una granja de servidores.

Una caché distribuida tiene varias ventajas con respecto a otros escenarios de almacenamiento en caché en los que los datos almacenados en caché se almacenan en servidores de aplicaciones individuales.

Cuando se distribuyen los datos almacenados en caché, los datos:

* Es *coherente (coherente)* entre las solicitudes a varios servidores.
* Sobrevive los reinicios del servidor y las implementaciones de aplicaciones.
* No usa la memoria local.

La configuración de caché distribuida es específica de la implementación. En este artículo se describe cómo configurar SQL Server y las cachés distribuidas de Redis. También hay implementaciones de terceros disponibles, como [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache)). Independientemente de la implementación seleccionada, la aplicación interactúa con la memoria caché mediante la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Para usar una SQL Server caché distribuida, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar una caché distribuida de Redis, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) y agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . El paquete de Redis no está incluido en el `Microsoft.AspNetCore.App` paquete, por lo que debe hacer referencia al paquete de Redis por separado en el archivo de proyecto.

Para usar caché distribuida de NCache, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) y agregue una referencia de paquete al paquete [NCache. Microsoft. Extensions. Caching. código abierto](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . El paquete de NCache no está incluido en el `Microsoft.AspNetCore.App` paquete, por lo que debe hacer referencia al paquete de NCache por separado en el archivo del proyecto.

## <a name="idistributedcache-interface"></a>Interfaz IDistributedCache

La <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz proporciona los métodos siguientes para manipular los elementos de la implementación de caché distribuida:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Acepta una clave de cadena y recupera un elemento almacenado en memoria caché como una `byte[]` matriz, si se encuentra en la memoria caché.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Agrega un elemento (como `byte[]` matriz) a la memoria caché utilizando una clave de cadena.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualiza un elemento en la memoria caché en función de su clave, restableciendo el tiempo de espera de expiración variable (si existe).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Quita un elemento de la memoria caché en función de su clave de cadena.

## <a name="establish-distributed-caching-services"></a>Establecer servicios de almacenamiento en caché distribuidos

Registre una implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en `Startup.ConfigureServices` . Las implementaciones proporcionadas por el marco de trabajo que se describen en este tema incluyen:

* [Caché de memoria distribuida](#distributed-memory-cache)
* [Caché de SQL Server distribuida](#distributed-sql-server-cache)
* [Caché en Redis distribuida](#distributed-redis-cache)
* [Caché de NCache distribuida](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Caché de memoria distribuida

La memoria caché de memoria distribuida ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) es una implementación proporcionada por el marco de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que almacena elementos en la memoria. La caché de memoria distribuida no es una caché distribuida real. Los elementos almacenados en caché se almacenan en la instancia de la aplicación en el servidor donde se ejecuta la aplicación.

La caché de memoria distribuida es una implementación útil:

* En escenarios de desarrollo y pruebas.
* Cuando se usa un solo servidor en producción y el consumo de memoria no es un problema. La implementación de la caché de memoria distribuida abstrae el almacenamiento de datos en caché. Permite implementar una solución de almacenamiento en caché distribuida verdadera en el futuro si se necesitan varios nodos o tolerancia a errores.

La aplicación de ejemplo hace uso de la caché de memoria distribuida cuando la aplicación se ejecuta en el entorno de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Caché de SQL Server distribuida

La implementación de la memoria caché de SQL Server distribuida ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que la memoria caché distribuida use una base de datos SQL Server como su memoria auxiliar. Para crear una SQL Server tabla de elementos almacenados en caché en una instancia de SQL Server, puede usar la `sql-cache` herramienta. La herramienta crea una tabla con el nombre y el esquema que especifique.

Cree una tabla en SQL Server ejecutando el `sql-cache create` comando. Proporcione la SQL Server instancia ( `Data Source` ), la base de datos ( `Initial Catalog` ), el esquema (por ejemplo, `dbo` ) y el nombre de la tabla (por ejemplo, `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Se registra un mensaje para indicar que la herramienta se ha realizado correctamente:

```console
Table and index were created successfully.
```

La tabla creada por la `sql-cache` herramienta tiene el siguiente esquema:

![Tabla de caché SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Una aplicación debe manipular los valores de la memoria caché mediante una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , no un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

La aplicación de ejemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> en un entorno que no es de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (y, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> y <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se almacena normalmente fuera del control de código fuente (por ejemplo, lo almacena el [Administrador secreto](xref:security/app-secrets) o en *appsettings.jsen* / *appSettings. { ENVIRONMENT}. JSON* files). La cadena de conexión puede contener credenciales que deben mantenerse fuera de los sistemas de control de código fuente.

### <a name="distributed-redis-cache"></a>Redis Cache distribuido

[Redis](https://redis.io/) es un almacén de datos en memoria de código abierto, que a menudo se usa como caché distribuida. Puede usar Redis localmente, y puede configurar un [Azure Redis cache](https://azure.microsoft.com/services/cache/) para una aplicación de ASP.net Core hospedada en Azure.

Una aplicación configura la implementación de la memoria caché mediante una <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instancia <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> de () en un entorno que no es de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Para instalar Redis en el equipo local:

1. Instale el [paquete chocolatey Redis](https://chocolatey.org/packages/redis-64/).
1. Ejecute `redis-server` desde un símbolo del sistema.

### <a name="distributed-ncache-cache"></a>Caché de NCache distribuida

[NCache](https://github.com/Alachisoft/NCache) es una caché distribuida en memoria de código abierto desarrollada de forma nativa en .NET y .net Core. NCache funciona localmente y se configura como un clúster de caché distribuida para una aplicación ASP.NET Core que se ejecuta en Azure o en otras plataformas de hospedaje.

Para instalar y configurar NCache en el equipo local, consulte [Guía de introducción de NCache para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Para configurar NCache:

1. Instalación de [NuGet de código abierto de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configure el clúster de caché en [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Agregue el siguiente código a `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Usar la memoria caché distribuida

Para usar la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz, solicite una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> desde cualquier constructor de la aplicación. La instancia se proporciona mediante la [inserción de dependencias (di)](xref:fundamentals/dependency-injection).

Cuando se inicia la aplicación de ejemplo, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> se inserta en `Startup.Configure` . La hora actual se almacena en la memoria caché mediante <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obtener más información, vea [host de Web: interfaz IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

La aplicación de ejemplo inyecta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en el `IndexModel` para su uso en la página de índice.

Cada vez que se carga la página de índice, se comprueba la hora almacenada en caché en `OnGetAsync` . Si la hora almacenada en caché no ha expirado, se muestra la hora. Si han transcurrido 20 segundos desde la última vez que se tuvo acceso a la hora almacenada en caché (la última vez que se cargó esta página), la página muestra la *fecha de expiración de la memoria caché*.

Actualice inmediatamente la hora almacenada en caché a la hora actual; para ello, seleccione el botón **restablecer hora de almacenamiento en caché** . El botón desencadena el `OnPostResetCachedTime` método de control.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> No es necesario usar un singleton o una duración de ámbito para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> las instancias (al menos para las implementaciones integradas).
>
> También puede crear una <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instancia siempre que necesite una en lugar de usar di, pero la creación de una instancia de en el código puede hacer que el código sea más difícil de probar e infringe el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recomendaciones

A la hora de decidir qué implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> es la más adecuada para su aplicación, tenga en cuenta lo siguiente:

* Infraestructura existente
* Requisitos de rendimiento
* Coste
* Experiencia del equipo

Las soluciones de almacenamiento en caché suelen basarse en el almacenamiento en memoria para proporcionar una recuperación rápida de los datos almacenados en memoria caché, pero la memoria es un recurso limitado y se amplía de forma costosa. Almacene solo los datos usados habitualmente en una memoria caché.

Por lo general, una caché en Redis proporciona un mayor rendimiento y una menor latencia que una memoria caché de SQL Server. Sin embargo, normalmente es necesario realizar pruebas comparativas para determinar las características de rendimiento de las estrategias de almacenamiento en caché.

Cuando SQL Server se usa como un almacén de respaldo de caché distribuida, el uso de la misma base de datos para la memoria caché y el almacenamiento y la recuperación de datos normales de la aplicación puede afectar negativamente al rendimiento de ambos. Se recomienda usar una instancia de SQL Server dedicada para el almacén de respaldo de caché distribuida.

## <a name="additional-resources"></a>Recursos adicionales

* [Redis Cache en Azure](/azure/azure-cache-for-redis/)
* [SQL Database en Azure](/azure/sql-database/)
* [ASP.net Core del proveedor de IDistributedCache para NCache en granjas de servidores web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Una caché distribuida es una memoria caché compartida por varios servidores de aplicaciones, que normalmente se mantiene como un servicio externo a los servidores de aplicaciones que tienen acceso a ella. Una caché distribuida puede mejorar el rendimiento y la escalabilidad de una aplicación ASP.NET Core, especialmente cuando la aplicación está hospedada en un servicio en la nube o en una granja de servidores.

Una caché distribuida tiene varias ventajas con respecto a otros escenarios de almacenamiento en caché en los que los datos almacenados en caché se almacenan en servidores de aplicaciones individuales.

Cuando se distribuyen los datos almacenados en caché, los datos:

* Es *coherente (coherente)* entre las solicitudes a varios servidores.
* Sobrevive los reinicios del servidor y las implementaciones de aplicaciones.
* No usa la memoria local.

La configuración de caché distribuida es específica de la implementación. En este artículo se describe cómo configurar SQL Server y las cachés distribuidas de Redis. También hay implementaciones de terceros disponibles, como [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache)). Independientemente de la implementación seleccionada, la aplicación interactúa con la memoria caché mediante la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Para usar una SQL Server caché distribuida, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Para usar una caché distribuida de Redis, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) y agregue una referencia de paquete al paquete [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . El paquete de Redis no está incluido en el `Microsoft.AspNetCore.App` paquete, por lo que debe hacer referencia al paquete de Redis por separado en el archivo de proyecto.

Para usar caché distribuida de NCache, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) y agregue una referencia de paquete al paquete [NCache. Microsoft. Extensions. Caching. código abierto](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . El paquete de NCache no está incluido en el `Microsoft.AspNetCore.App` paquete, por lo que debe hacer referencia al paquete de NCache por separado en el archivo del proyecto.

## <a name="idistributedcache-interface"></a>Interfaz IDistributedCache

La <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz proporciona los métodos siguientes para manipular los elementos de la implementación de caché distribuida:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Acepta una clave de cadena y recupera un elemento almacenado en memoria caché como una `byte[]` matriz, si se encuentra en la memoria caché.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Agrega un elemento (como `byte[]` matriz) a la memoria caché utilizando una clave de cadena.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualiza un elemento en la memoria caché en función de su clave, restableciendo el tiempo de espera de expiración variable (si existe).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Quita un elemento de la memoria caché en función de su clave de cadena.

## <a name="establish-distributed-caching-services"></a>Establecer servicios de almacenamiento en caché distribuidos

Registre una implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en `Startup.ConfigureServices` . Las implementaciones proporcionadas por el marco de trabajo que se describen en este tema incluyen:

* [Caché de memoria distribuida](#distributed-memory-cache)
* [Caché de SQL Server distribuida](#distributed-sql-server-cache)
* [Caché en Redis distribuida](#distributed-redis-cache)
* [Caché de NCache distribuida](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Caché de memoria distribuida

La memoria caché de memoria distribuida ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) es una implementación proporcionada por el marco de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que almacena elementos en la memoria. La caché de memoria distribuida no es una caché distribuida real. Los elementos almacenados en caché se almacenan en la instancia de la aplicación en el servidor donde se ejecuta la aplicación.

La caché de memoria distribuida es una implementación útil:

* En escenarios de desarrollo y pruebas.
* Cuando se usa un solo servidor en producción y el consumo de memoria no es un problema. La implementación de la caché de memoria distribuida abstrae el almacenamiento de datos en caché. Permite implementar una solución de almacenamiento en caché distribuida verdadera en el futuro si se necesitan varios nodos o tolerancia a errores.

La aplicación de ejemplo hace uso de la caché de memoria distribuida cuando la aplicación se ejecuta en el entorno de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Caché de SQL Server distribuida

La implementación de la memoria caché de SQL Server distribuida ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que la memoria caché distribuida use una base de datos SQL Server como su memoria auxiliar. Para crear una SQL Server tabla de elementos almacenados en caché en una instancia de SQL Server, puede usar la `sql-cache` herramienta. La herramienta crea una tabla con el nombre y el esquema que especifique.

Cree una tabla en SQL Server ejecutando el `sql-cache create` comando. Proporcione la SQL Server instancia ( `Data Source` ), la base de datos ( `Initial Catalog` ), el esquema (por ejemplo, `dbo` ) y el nombre de la tabla (por ejemplo, `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Se registra un mensaje para indicar que la herramienta se ha realizado correctamente:

```console
Table and index were created successfully.
```

La tabla creada por la `sql-cache` herramienta tiene el siguiente esquema:

![Tabla de caché SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Una aplicación debe manipular los valores de la memoria caché mediante una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , no un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

La aplicación de ejemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> en un entorno que no es de desarrollo en `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (y, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> y <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se almacena normalmente fuera del control de código fuente (por ejemplo, lo almacena el [Administrador secreto](xref:security/app-secrets) o en *appsettings.jsen* / *appSettings. { ENVIRONMENT}. JSON* files). La cadena de conexión puede contener credenciales que deben mantenerse fuera de los sistemas de control de código fuente.

### <a name="distributed-redis-cache"></a>Redis Cache distribuido

[Redis](https://redis.io/) es un almacén de datos en memoria de código abierto, que a menudo se usa como caché distribuida. Puede usar Redis localmente, y puede configurar un [Azure Redis cache](https://azure.microsoft.com/services/cache/) para una aplicación de ASP.net Core hospedada en Azure.

Una aplicación configura la implementación de la memoria caché mediante una <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instancia de ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Para instalar Redis en el equipo local:

1. Instale el [paquete chocolatey Redis](https://chocolatey.org/packages/redis-64/).
1. Ejecute `redis-server` desde un símbolo del sistema.

### <a name="distributed-ncache-cache"></a>Caché de NCache distribuida

[NCache](https://github.com/Alachisoft/NCache) es una caché distribuida en memoria de código abierto desarrollada de forma nativa en .NET y .net Core. NCache funciona localmente y se configura como un clúster de caché distribuida para una aplicación ASP.NET Core que se ejecuta en Azure o en otras plataformas de hospedaje.

Para instalar y configurar NCache en el equipo local, consulte [Guía de introducción de NCache para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Para configurar NCache:

1. Instalación de [NuGet de código abierto de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configure el clúster de caché en [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Agregue el siguiente código a `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Usar la memoria caché distribuida

Para usar la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaz, solicite una instancia de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> desde cualquier constructor de la aplicación. La instancia se proporciona mediante la [inserción de dependencias (di)](xref:fundamentals/dependency-injection).

Cuando se inicia la aplicación de ejemplo, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> se inserta en `Startup.Configure` . La hora actual se almacena en la memoria caché mediante <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obtener más información, vea [host de Web: interfaz IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

La aplicación de ejemplo inyecta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> en el `IndexModel` para su uso en la página de índice.

Cada vez que se carga la página de índice, se comprueba la hora almacenada en caché en `OnGetAsync` . Si la hora almacenada en caché no ha expirado, se muestra la hora. Si han transcurrido 20 segundos desde la última vez que se tuvo acceso a la hora almacenada en caché (la última vez que se cargó esta página), la página muestra la *fecha de expiración de la memoria caché*.

Actualice inmediatamente la hora almacenada en caché a la hora actual; para ello, seleccione el botón **restablecer hora de almacenamiento en caché** . El botón desencadena el `OnPostResetCachedTime` método de control.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> No es necesario usar un singleton o una duración de ámbito para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> las instancias (al menos para las implementaciones integradas).
>
> También puede crear una <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instancia siempre que necesite una en lugar de usar di, pero la creación de una instancia de en el código puede hacer que el código sea más difícil de probar e infringe el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recomendaciones

A la hora de decidir qué implementación de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> es la más adecuada para su aplicación, tenga en cuenta lo siguiente:

* Infraestructura existente
* Requisitos de rendimiento
* Coste
* Experiencia del equipo

Las soluciones de almacenamiento en caché suelen basarse en el almacenamiento en memoria para proporcionar una recuperación rápida de los datos almacenados en memoria caché, pero la memoria es un recurso limitado y se amplía de forma costosa. Almacene solo los datos usados habitualmente en una memoria caché.

Por lo general, una caché en Redis proporciona un mayor rendimiento y una menor latencia que una memoria caché de SQL Server. Sin embargo, normalmente es necesario realizar pruebas comparativas para determinar las características de rendimiento de las estrategias de almacenamiento en caché.

Cuando SQL Server se usa como un almacén de respaldo de caché distribuida, el uso de la misma base de datos para la memoria caché y el almacenamiento y la recuperación de datos normales de la aplicación puede afectar negativamente al rendimiento de ambos. Se recomienda usar una instancia de SQL Server dedicada para el almacén de respaldo de caché distribuida.

## <a name="additional-resources"></a>Recursos adicionales

* [Redis Cache en Azure](/azure/azure-cache-for-redis/)
* [SQL Database en Azure](/azure/sql-database/)
* [ASP.net Core del proveedor de IDistributedCache para NCache en granjas de servidores web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache en github](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 